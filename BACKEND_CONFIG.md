# Configuracion Backend - 14 Agentes AMPI

## Resumen de componentes

1. **Google Sheets** - Base de datos de registros
2. **Google Drive** - Almacenamiento de fotos
3. **Google Apps Script** - API que conecta todo

---

## PASO 1: Crear Google Sheets

### 1.1 Crear nueva hoja de calculo

1. Ve a [Google Sheets](https://sheets.google.com)
2. Crea una nueva hoja: **"14 Agentes AMPI - Registros"**
3. Renombra la primera pestana a: **"Registros"**

### 1.2 Crear encabezados (Fila 1)

Copia estos encabezados exactamente en la fila 1:

| A | B | C | D | E | F | G | H | I | J | K | L | M | N | O | P | Q |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Timestamp | Nombre | Telefono | Agencia | Ciudad | Instagram | Facebook | TikTok | SitioWeb | TipoPropiedad | Operacion | Colonias | Descripcion | FotoURL | AutorizacionEditorial | AvisoPrivacidad | RegistroCompleto |

### 1.3 Crear pestana "Configuracion"

1. Agrega una nueva pestana llamada **"Configuracion"**
2. En la celda A1 escribe: `CupoMaximo`
3. En la celda B1 escribe: `14`
4. En la celda A2 escribe: `FormularioActivo`
5. En la celda B2 escribe: `TRUE`

Esto te permite cambiar el cupo o desactivar el formulario sin tocar codigo.

---

## PASO 2: Crear carpeta en Google Drive

1. Ve a [Google Drive](https://drive.google.com)
2. Crea una carpeta llamada: **"Fotos_14_Agentes_AMPI"**
3. Click derecho > Compartir > Configuracion avanzada
4. Cambia a: **"Cualquier persona con el enlace puede ver"**
5. **IMPORTANTE:** Copia el ID de la carpeta de la URL:
   ```
   https://drive.google.com/drive/folders/XXXXXXXXXXXXXXXXXXXXXX
                                          ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
                                          Este es el FOLDER_ID
   ```

---

## PASO 3: Crear Google Apps Script

### 3.1 Abrir el editor

1. En tu Google Sheets, ve a **Extensiones > Apps Script**
2. Elimina todo el codigo que aparece por defecto
3. Copia el codigo completo de abajo

### 3.2 Codigo del Apps Script

```javascript
// ============================================================
// CONFIGURACION - MODIFICAR ESTOS VALORES
// ============================================================
const SPREADSHEET_ID = 'TU_SPREADSHEET_ID_AQUI'; // ID de tu Google Sheets
const FOLDER_ID = 'TU_FOLDER_ID_AQUI';           // ID de la carpeta en Drive
const SHEET_NAME = 'Registros';                   // Nombre de la pestana
const CONFIG_SHEET = 'Configuracion';             // Pestana de configuracion

// ============================================================
// FUNCION PRINCIPAL - NO MODIFICAR
// ============================================================
function doPost(e) {
  try {
    // Verificar si el formulario esta activo
    const config = getConfig();
    if (!config.formularioActivo) {
      return createResponse(false, 'El formulario esta temporalmente desactivado.');
    }

    // Verificar cupo
    const registrosActuales = contarRegistrosCompletos();
    if (registrosActuales >= config.cupoMaximo) {
      return createResponse(false, 'CUPO_LLENO', {
        mensaje: 'El cupo de esta edicion se ha completado.',
        registros: registrosActuales
      });
    }

    // Obtener datos del formulario
    const data = e.parameter;

    // Validar campos requeridos
    const camposRequeridos = ['nombre', 'telefono', 'agencia', 'ciudad',
                              'instagram', 'facebook', 'tiktok',
                              'tipo_propiedad', 'operacion', 'colonias'];

    for (const campo of camposRequeridos) {
      if (!data[campo] || data[campo].trim() === '') {
        return createResponse(false, `El campo ${campo} es requerido.`);
      }
    }

    // Verificar duplicados por telefono
    if (existeTelefono(data.telefono)) {
      return createResponse(false, 'DUPLICADO', {
        mensaje: 'Ya existe un registro con este numero de telefono.'
      });
    }

    // Procesar foto si existe
    let fotoURL = '';
    if (e.parameter.foto_base64 && e.parameter.foto_nombre) {
      fotoURL = guardarFotoEnDrive(
        e.parameter.foto_base64,
        e.parameter.foto_nombre,
        data.nombre,
        data.telefono
      );
    }

    // Guardar en Sheets
    const sheet = SpreadsheetApp.openById(SPREADSHEET_ID).getSheetByName(SHEET_NAME);
    const timestamp = new Date().toLocaleString('es-MX', {timeZone: 'America/Mexico_City'});

    const registroCompleto = fotoURL !== '' ? 'SI' : 'NO';

    sheet.appendRow([
      timestamp,
      data.nombre,
      data.telefono,
      data.agencia,
      data.ciudad,
      data.instagram,
      data.facebook,
      data.tiktok,
      data.sitioweb || '',
      data.tipo_propiedad,
      data.operacion,
      data.colonias,
      data.descripcion || '',
      fotoURL,
      data.autorizacion_editorial === 'true' ? 'SI' : 'NO',
      data.aviso_privacidad === 'true' ? 'SI' : 'NO',
      registroCompleto
    ]);

    // Contar nuevamente para informar cuantos lugares quedan
    const nuevosRegistros = contarRegistrosCompletos();
    const lugaresRestantes = config.cupoMaximo - nuevosRegistros;

    return createResponse(true, 'Registro exitoso', {
      nombre: data.nombre,
      lugaresRestantes: lugaresRestantes,
      registroNumero: nuevosRegistros
    });

  } catch (error) {
    console.error('Error en doPost:', error);
    return createResponse(false, 'Error interno del servidor: ' + error.message);
  }
}

// ============================================================
// FUNCIONES AUXILIARES
// ============================================================

function getConfig() {
  const sheet = SpreadsheetApp.openById(SPREADSHEET_ID).getSheetByName(CONFIG_SHEET);
  return {
    cupoMaximo: parseInt(sheet.getRange('B1').getValue()) || 14,
    formularioActivo: sheet.getRange('B2').getValue() === true ||
                      sheet.getRange('B2').getValue() === 'TRUE'
  };
}

function contarRegistrosCompletos() {
  const sheet = SpreadsheetApp.openById(SPREADSHEET_ID).getSheetByName(SHEET_NAME);
  const data = sheet.getDataRange().getValues();

  let contador = 0;
  // Empezar desde fila 2 (indice 1) para saltar encabezados
  for (let i = 1; i < data.length; i++) {
    // Columna Q (indice 16) = RegistroCompleto
    if (data[i][16] === 'SI') {
      contador++;
    }
  }
  return contador;
}

function existeTelefono(telefono) {
  const sheet = SpreadsheetApp.openById(SPREADSHEET_ID).getSheetByName(SHEET_NAME);
  const data = sheet.getDataRange().getValues();

  const telefonoLimpio = telefono.replace(/\D/g, '');

  for (let i = 1; i < data.length; i++) {
    const telExistente = String(data[i][2]).replace(/\D/g, '');
    if (telExistente === telefonoLimpio) {
      return true;
    }
  }
  return false;
}

function guardarFotoEnDrive(base64Data, nombreOriginal, nombreAgente, telefono) {
  try {
    // Limpiar el base64 (remover el prefijo data:image/xxx;base64,)
    const base64Clean = base64Data.replace(/^data:image\/\w+;base64,/, '');

    // Decodificar
    const blob = Utilities.newBlob(
      Utilities.base64Decode(base64Clean),
      'image/jpeg',
      generarNombreArchivo(nombreAgente, telefono, nombreOriginal)
    );

    // Guardar en Drive
    const folder = DriveApp.getFolderById(FOLDER_ID);
    const file = folder.createFile(blob);

    // Hacer el archivo publico
    file.setSharing(DriveApp.Access.ANYONE_WITH_LINK, DriveApp.Permission.VIEW);

    // Retornar URL directa de la imagen
    return 'https://drive.google.com/uc?id=' + file.getId();

  } catch (error) {
    console.error('Error al guardar foto:', error);
    return '';
  }
}

function generarNombreArchivo(nombre, telefono, original) {
  // Limpiar nombre (remover acentos y caracteres especiales)
  const nombreLimpio = nombre
    .normalize('NFD')
    .replace(/[\u0300-\u036f]/g, '')
    .replace(/[^a-zA-Z0-9]/g, '_')
    .substring(0, 30);

  const telefonoLimpio = telefono.replace(/\D/g, '');

  // Obtener extension del archivo original
  const extension = original.split('.').pop().toLowerCase() || 'jpg';

  return `${nombreLimpio}_${telefonoLimpio}.${extension}`;
}

function createResponse(success, message, data = {}) {
  const response = {
    success: success,
    message: message,
    data: data
  };

  return ContentService
    .createTextOutput(JSON.stringify(response))
    .setMimeType(ContentService.MimeType.JSON);
}

// ============================================================
// FUNCION PARA VERIFICAR CUPO (GET REQUEST)
// ============================================================
function doGet(e) {
  try {
    const config = getConfig();
    const registros = contarRegistrosCompletos();
    const lugaresDisponibles = config.cupoMaximo - registros;

    return createResponse(true, 'Estado del cupo', {
      cupoMaximo: config.cupoMaximo,
      registrosActuales: registros,
      lugaresDisponibles: lugaresDisponibles,
      formularioActivo: config.formularioActivo,
      cupoLleno: lugaresDisponibles <= 0
    });

  } catch (error) {
    return createResponse(false, 'Error: ' + error.message);
  }
}
```

### 3.3 Configurar los IDs

En el codigo, reemplaza:

1. **`TU_SPREADSHEET_ID_AQUI`** - El ID de tu Google Sheets
   - URL: `https://docs.google.com/spreadsheets/d/XXXXXXXXXXXXXXX/edit`
   - El ID es la parte `XXXXXXXXXXXXXXX`

2. **`TU_FOLDER_ID_AQUI`** - El ID de la carpeta de Drive (del Paso 2)

### 3.4 Desplegar como Web App

1. Click en **Implementar > Nueva implementacion**
2. Selecciona tipo: **Aplicacion web**
3. Configura:
   - Descripcion: "14 Agentes AMPI - Form API"
   - Ejecutar como: **Yo (tu email)**
   - Quien tiene acceso: **Cualquier persona**
4. Click en **Implementar**
5. **Autoriza** los permisos cuando te lo pida
6. **COPIA LA URL** que te da - la necesitaras para el frontend

La URL sera algo como:
```
https://script.google.com/macros/s/XXXXXXXXXXXXXXXXXXXXXX/exec
```

---

## PASO 4: Permisos necesarios

Cuando despliegues, Google te pedira autorizar estos permisos:

- Ver y administrar hojas de calculo
- Ver y administrar archivos de Google Drive

Esto es normal y necesario para que funcione.

---

## PASO 5: Probar el endpoint

### Verificar estado del cupo (GET)

Abre en el navegador la URL de tu Web App:
```
https://script.google.com/macros/s/TU_ID/exec
```

Deberia devolver algo como:
```json
{
  "success": true,
  "message": "Estado del cupo",
  "data": {
    "cupoMaximo": 14,
    "registrosActuales": 0,
    "lugaresDisponibles": 14,
    "formularioActivo": true,
    "cupoLleno": false
  }
}
```

---

## PASO 6: Actualizar el Frontend

En el `index.html`, actualiza:

### 6.1 La URL del formulario

Cambia la linea 196 (aproximadamente):
```html
<form id="FormCurso" action="URL_ANTERIOR" ...>
```

Por:
```html
<form id="Form14Agentes" action="TU_NUEVA_URL_APPS_SCRIPT" ...>
```

### 6.2 El JavaScript de envio

Reemplaza el script al final del HTML con este codigo actualizado:

```javascript
// Verificar cupo al cargar la pagina
document.addEventListener('DOMContentLoaded', function() {
  verificarCupo();
});

async function verificarCupo() {
  try {
    const response = await fetch('TU_URL_APPS_SCRIPT');
    const data = await response.json();

    if (data.data.cupoLleno) {
      mostrarCupoLleno();
    } else {
      actualizarContador(data.data.lugaresDisponibles);
    }
  } catch (error) {
    console.error('Error verificando cupo:', error);
  }
}

function mostrarCupoLleno() {
  const form = document.getElementById('Form14Agentes');
  form.innerHTML = `
    <div class="cupo-lleno-mensaje">
      <h3>Cupo completo</h3>
      <p>Gracias por tu interes. El cupo de esta edicion se ha completado.</p>
      <p>Puedes registrarte a lista de espera para la siguiente edicion.</p>
      <button class="ed-btn" onclick="mostrarListaEspera()">Lista de espera</button>
    </div>
  `;
}

function actualizarContador(lugares) {
  const contador = document.getElementById('lugares-disponibles');
  if (contador) {
    contador.textContent = lugares;
  }
}

// Manejo del formulario
document.getElementById("Form14Agentes").addEventListener("submit", async function(event) {
  event.preventDefault();

  const form = event.target;
  const submitBtn = document.getElementById("btn-submit");
  const originalBtnHtml = submitBtn.innerHTML;

  // Deshabilitar boton
  submitBtn.disabled = true;
  submitBtn.innerText = "Enviando...";

  // Mostrar loading
  Swal.fire({
    title: "Enviando tu postulacion...",
    text: "Por favor espera un momento.",
    allowOutsideClick: false,
    allowEscapeKey: false,
    showConfirmButton: false,
    didOpen: () => Swal.showLoading()
  });

  try {
    // Preparar datos
    const formData = new FormData(form);

    // Convertir foto a base64
    const fotoInput = document.getElementById('foto');
    if (fotoInput.files[0]) {
      const base64 = await fileToBase64(fotoInput.files[0]);
      formData.append('foto_base64', base64);
      formData.append('foto_nombre', fotoInput.files[0].name);
    }

    // Convertir checkboxes a string
    const tiposPropiedad = [];
    document.querySelectorAll('input[name="tipo_propiedad"]:checked').forEach(cb => {
      tiposPropiedad.push(cb.value);
    });
    formData.set('tipo_propiedad', tiposPropiedad.join(', '));

    const operaciones = [];
    document.querySelectorAll('input[name="operacion"]:checked').forEach(cb => {
      operaciones.push(cb.value);
    });
    formData.set('operacion', operaciones.join(', '));

    // Enviar
    const response = await fetch(form.action, {
      method: 'POST',
      body: formData
    });

    const result = await response.json();
    Swal.close();

    if (result.success) {
      Swal.fire({
        title: "¡Postulacion recibida!",
        html: `
          <p>Gracias <b>${result.data.nombre}</b>.</p>
          <p>Tu postulacion para <b>14 Agentes AMPI</b> se envio correctamente.</p>
          <p>Te contactaremos si tu perfil queda seleccionado.</p>
          <p class="lugares-info">Lugares restantes: <b>${result.data.lugaresRestantes}</b></p>
        `,
        icon: "success",
        confirmButtonText: "Aceptar",
        confirmButtonColor: "#00de81"
      });
      form.reset();
      actualizarContador(result.data.lugaresRestantes);

    } else if (result.message === 'CUPO_LLENO') {
      Swal.fire({
        title: "Cupo completo",
        text: result.data.mensaje,
        icon: "info",
        confirmButtonText: "Entendido"
      });
      mostrarCupoLleno();

    } else if (result.message === 'DUPLICADO') {
      Swal.fire({
        title: "Registro existente",
        text: result.data.mensaje,
        icon: "warning",
        confirmButtonText: "Entendido"
      });

    } else {
      throw new Error(result.message);
    }

  } catch (error) {
    Swal.close();
    Swal.fire({
      title: "Error",
      text: "Ocurrio un problema al enviar tu postulacion. Intenta nuevamente.",
      icon: "error",
      confirmButtonText: "Aceptar"
    });
    console.error("Error:", error);

  } finally {
    submitBtn.disabled = false;
    submitBtn.innerHTML = originalBtnHtml;
  }
});

// Utilidad para convertir archivo a base64
function fileToBase64(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => resolve(reader.result);
    reader.onerror = error => reject(error);
  });
}
```

---

## Resumen de lo que hace el backend

| Funcion | Descripcion |
|---------|-------------|
| Recibir datos | Valida campos requeridos |
| Verificar cupo | Cuenta registros completos, bloquea si >= 14 |
| Detectar duplicados | Busca telefono existente |
| Subir foto | Guarda en Drive, retorna URL publica |
| Guardar registro | Agrega fila a Sheets con timestamp |
| Verificar estado | Endpoint GET para consultar cupo |

---

## Administracion

### Desactivar formulario temporalmente
En la pestana "Configuracion", cambia B2 a `FALSE`

### Cambiar cupo
En la pestana "Configuracion", cambia B1 al nuevo numero

### Ver registros
Abre la pestana "Registros" en Google Sheets

### Ver fotos
Abre la carpeta "Fotos_14_Agentes_AMPI" en Google Drive

---

## Troubleshooting

| Problema | Solucion |
|----------|----------|
| Error de permisos | Re-autoriza el Apps Script |
| Fotos no se suben | Verifica FOLDER_ID y permisos de carpeta |
| Cupo no funciona | Verifica que la columna Q tenga "SI" o "NO" |
| Duplicados no detecta | Verifica formato de telefono (solo numeros) |

---

*Documento generado el 21 de enero de 2026*
