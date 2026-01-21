# Documentacion para Desarrollador - Landing "14 Agentes AMPI"

## Resumen del Proyecto

**Objetivo:** Transformar la landing page actual (curso CIPS) en un formulario de postulacion para que agentes inmobiliarios aparezcan en la revista "14 Agentes AMPI".

**Alcance del trabajo:** Solo frontend (HTML/CSS). La configuracion del backend (Google Sheets, etc.) la hara el equipo interno.

---

## 1. Estructura del Proyecto

```
Form14AgentesAMPI/
├── index.html              ← ARCHIVO PRINCIPAL A MODIFICAR
├── assets/
│   ├── css/style.css       ← Estilos custom (no modificar mucho)
│   ├── js/
│   │   ├── main.js         ← JS principal
│   │   └── accordion.js    ← FAQ (puede eliminarse si no se usa)
│   ├── img/                ← Imagenes (agregar nuevas aqui)
│   └── vendor/             ← Librerias (Swiper, FontAwesome, etc.)
├── src/output.css          ← Tailwind compilado (NO MODIFICAR)
```

---

## 2. Que MANTENER (No Modificar)

### Header (lineas 67-85 en index.html)
- Logo AMPI Riviera Nayarit
- Enlace "Visitar Sitio Oficial"
- Funcionalidad mobile sidebar
- Colores y estilos

### Footer (lineas 546-598 en index.html)
- Logo y descripcion AMPI
- Datos de contacto (+52 322 297 5322, ampi.nayarit@gmail.com)
- Redes sociales
- Copyright y terminos

### Estilos Generales
- Paleta de colores (azul oscuro `#241442`, verde `#00de81`)
- Tipografia Poppins
- Clases de Tailwind existentes

---

## 3. Que MODIFICAR/REEMPLAZAR

### A) BANNER SECTION (lineas 89-134)

**Contenido ACTUAL a reemplazar:**
- Titulo: "Certificate como Especialista en Propiedades Internacionales (CIPS)"
- Subtitulo: "ONLINE CERTIFICATION · REALTOR INTERNACIONAL"

**Contenido NUEVO:**

```html
<!-- TITULO PRINCIPAL (H1) -->✅
<h1>Postulate para aparecer en "14 Agentes AMPI" - Proxima edicion</h1>

<!-- SUBTITULO -->✅
<p class="ed-section-sub-title">REVISTA AMPI RIVIERA NAYARIT</p>

<!-- TEXTO DESCRIPTIVO -->✅
<p>Comparte tu perfil profesional para que el publico te conozca. Publicaremos
14 perfiles con: foto, nombre, telefono, agencia, redes sociales y especialidad.</p>

<!-- AVISO DE CUPO (destacado) -->✅
<div class="cupo-limitado">
  <strong>Cupo limitado:</strong> solo 14 perfiles seran publicados.
  Consideraremos los primeros 14 registros completos.
</div>

<!-- BOTONES CTA -->✅
<a href="#form" class="ed-btn">Quiero postularme</a>
<a href="#about" class="ed-btn ed-btn--white">Mas informacion</a>
```
✅
**Imagenes del banner:**
- Reemplazar las fotos actuales por imagenes relacionadas con agentes/revista
- Ubicacion: `assets/img/banner-2-img-1.jpg` y `banner-2-img-2.jpg` 
✅

---

### B) SECCION CONTACT/FORM (lineas 136-249)

Esta es la seccion MAS IMPORTANTE. Reemplazar completamente.

#### Lado Izquierdo - Informacion

**ELIMINAR:**
- Fechas del curso
- Horario
- Costo $11,500 MXN

**AGREGAR:**

```html
<div class="info-postulacion">
  <h3>¿Que es "14 Agentes AMPI"?</h3>
  <p>Una seccion especial en nuestra revista donde destacamos a 14 agentes
  inmobiliarios de la region. Tu perfil sera visto por miles de lectores
  interesados en bienes raices.</p>

  <h4>Tu perfil incluira:</h4>
  <ul>
    <li>Foto profesional</li>
    <li>Nombre completo</li>
    <li>Telefono de contacto</li>
    <li>Agencia inmobiliaria</li>
    <li>Redes sociales</li>
    <li>Especialidad (tipo de propiedad y zona)</li>
  </ul>

  <p class="nota-validacion">
    <strong>Nota:</strong> AMPI Riviera Nayarit se reserva el derecho de validar
    la informacion y el material enviado (foto legible, datos completos y correctos).
  </p>
</div>
```

#### Lado Derecho - FORMULARIO COMPLETO

**Titulo del formulario:**
```html
<h3>Completa tu postulacion</h3>
```

**CAMPOS DEL FORMULARIO (en orden):**

```html
<form id="form-postulacion">

  <!-- ========== SECCION 1: DATOS BASICOS ========== -->
  <h4>Datos basicos</h4>

  <!-- 1. Nombre completo -->
  <div class="form-group">
    <label for="nombre">Nombre completo *</label>
    <input type="text" id="nombre" name="nombre" required
           placeholder="Ej. Juan Perez Garcia">
  </div>

  <!-- 2. Telefono WhatsApp -->
  <div class="form-group">
    <label for="telefono">Telefono (WhatsApp) *</label>
    <input type="tel" id="telefono" name="telefono" required
           placeholder="10 digitos: 3221234567"
           pattern="[0-9]{10}"
           title="Ingresa 10 digitos sin espacios ni guiones">
  </div>

  <!-- 3. Agencia inmobiliaria -->
  <div class="form-group">
    <label for="agencia">Agencia inmobiliaria *</label>
    <input type="text" id="agencia" name="agencia" required
           placeholder="Ej. Century 21, RE/MAX, Independiente...">
  </div>

  <!-- 4. Ciudad/Zona (dropdown) -->
  <div class="form-group">
    <label for="ciudad">Ciudad / Zona general *</label>
    <select id="ciudad" name="ciudad" required>
      <option value="">Selecciona una opcion</option>
      <option value="Puerto Vallarta">Puerto Vallarta</option>
      <option value="Bahia de Banderas">Bahia de Banderas</option>
      <option value="Riviera Nayarit">Riviera Nayarit</option>
      <option value="Nuevo Vallarta">Nuevo Vallarta</option>
      <option value="Bucerias">Bucerias</option>
      <option value="Sayulita">Sayulita</option>
      <option value="San Pancho">San Pancho</option>
      <option value="Punta de Mita">Punta de Mita</option>
      <option value="Mezcales">Mezcales</option>
      <option value="Otra">Otra</option>
    </select>
  </div>

  <!-- ========== SECCION 2: REDES SOCIALES ========== -->
  <h4>Redes sociales</h4>

  <!-- 5. Instagram -->
  <div class="form-group">
    <label for="instagram">Instagram *</label>
    <input type="text" id="instagram" name="instagram" required
           placeholder="@tu_usuario o URL completa">
  </div>

  <!-- 6. Facebook -->
  <div class="form-group">
    <label for="facebook">Facebook *</label>
    <input type="text" id="facebook" name="facebook" required
           placeholder="URL de tu pagina o perfil">
  </div>

  <!-- 7. TikTok -->
  <div class="form-group">
    <label for="tiktok">TikTok *</label>
    <input type="text" id="tiktok" name="tiktok" required
           placeholder="@tu_usuario o URL">
  </div>

  <!-- 8. Sitio web (OPCIONAL) -->
  <div class="form-group">
    <label for="sitioweb">Sitio web (opcional)</label>
    <input type="url" id="sitioweb" name="sitioweb"
           placeholder="https://www.tusitio.com">
  </div>

  <!-- ========== SECCION 3: ESPECIALIDAD ========== -->
  <h4>Especialidad</h4>

  <!-- 9. Tipo de propiedad (multi-select con checkboxes) -->
  <div class="form-group">
    <label>Tipo de propiedad * (puedes elegir varios)</label>
    <div class="checkbox-group">
      <label><input type="checkbox" name="tipo_propiedad" value="Casas"> Casas</label>
      <label><input type="checkbox" name="tipo_propiedad" value="Departamentos"> Departamentos</label>
      <label><input type="checkbox" name="tipo_propiedad" value="Terrenos"> Terrenos</label>
      <label><input type="checkbox" name="tipo_propiedad" value="Comercial"> Comercial</label>
      <label><input type="checkbox" name="tipo_propiedad" value="Industrial"> Industrial</label>
    </div>
  </div>

  <!-- 10. Operacion (multi-select con checkboxes) -->
  <div class="form-group">
    <label>Operacion * (puedes elegir ambas)</label>
    <div class="checkbox-group">
      <label><input type="checkbox" name="operacion" value="Venta"> Venta</label>
      <label><input type="checkbox" name="operacion" value="Renta"> Renta</label>
    </div>
  </div>

  <!-- 11. Colonias/Zonas especificas (tags/texto) -->
  <div class="form-group">
    <label for="colonias">Colonias / Zonas especificas *</label>
    <input type="text" id="colonias" name="colonias" required
           placeholder="Ej. Marina Vallarta, Fluvial, Zona Romantica...">
    <small>Separa las zonas con comas</small>
  </div>

  <!-- 12. Descripcion breve (OPCIONAL) -->
  <div class="form-group">
    <label for="descripcion">Descripcion breve de tu especialidad (opcional)</label>
    <textarea id="descripcion" name="descripcion"
              maxlength="300" rows="3"
              placeholder="Me especializo en ___ (tipo), en ___ (zona), enfocado en ___ (rango/estilo)."></textarea>
    <small>Maximo 300 caracteres</small>
  </div>

  <!-- ========== SECCION 4: FOTO ========== -->
  <h4>Foto profesional</h4>

  <!-- 13. Subir foto -->
  <div class="form-group">
    <label for="foto">Subir foto *</label>
    <input type="file" id="foto" name="foto" required
           accept="image/jpeg,image/png">
    <small>
      Formatos: JPG o PNG | Peso max: 10 MB<br>
      Recomendacion: cuadrada 1:1, minimo 1080x1080 px, fondo limpio
    </small>
  </div>

  <!-- ========== SECCION 5: CONSENTIMIENTOS ========== -->
  <h4>Autorizaciones</h4>

  <!-- 14. Autorizacion uso editorial -->
  <div class="form-group checkbox-single">
    <label>
      <input type="checkbox" name="autorizacion_editorial" required>
      Autorizo el uso de mi foto y datos para fines editoriales y de difusion
      de AMPI Riviera Nayarit. *
    </label>
  </div>

  <!-- 15. Aviso de privacidad -->
  <div class="form-group checkbox-single">
    <label>
      <input type="checkbox" name="aviso_privacidad" required>
      Acepto el <a href="#" target="_blank">aviso de privacidad</a>. *
    </label>
  </div>

  <!-- Campo oculto para identificar el formulario -->
  <input type="hidden" name="form_id" value="postulacion_14_agentes">

  <!-- BOTON ENVIAR -->
  <button type="submit" class="ed-btn ed-btn--full">
    Enviar mi postulacion
  </button>

  <!-- Texto bajo el boton -->
  <p class="texto-envio">
    Al enviar confirmas que tu informacion es correcta y autorizas su uso editorial.
  </p>

</form>
```

---

### C) SECCION BENEFITS/CATEGORIES (lineas 252-362)

**Reemplazar las 3 tarjetas actuales por:**

| # | Icono sugerido | Titulo | Descripcion |
|---|----------------|--------|-------------|
| 1 | `fa-camera` o `fa-image` | Visibilidad profesional | Tu perfil sera visto por miles de lectores interesados en bienes raices |
| 2 | `fa-users` | Networking AMPI | Conecta con otros profesionales y potenciales clientes de la region |
| 3 | `fa-star` | Reconocimiento | Destaca como uno de los 14 agentes seleccionados de esta edicion |

---

### D) SECCION ABOUT (lineas 367-406)

**Titulo nuevo:**
```
"Se parte de los 14 Agentes destacados"
```

**Descripcion nueva:**
```
La revista de AMPI Riviera Nayarit es una publicacion de referencia para el
mercado inmobiliario de la region. En cada edicion, destacamos a 14 agentes
que representan lo mejor del sector.

Esta es tu oportunidad de aparecer frente a:
- Compradores potenciales de propiedades
- Inversionistas nacionales e internacionales
- Colegas del sector inmobiliario
- Publico general interesado en bienes raices
```

**Lista de beneficios (checkmarks):**
1. Exposicion en revista impresa y digital
2. Perfil profesional completo con foto
3. Tus datos de contacto visibles
4. Alcance regional garantizado

---

### E) SECCION STATS (lineas 414-447)

**Reemplazar estadisticas por datos relevantes:**

| Numero | Texto |
|--------|-------|
| 14 | Agentes por edicion |
| 5,000+ | Lectores de la revista |
| 100% | Perfil profesional |
| #1 | En la region |

*(Los numeros son sugeridos, ajustar segun datos reales)*

---

### F) SECCION FAQ (lineas 449-520)

**Reemplazar preguntas por:**

**Pregunta 1:** ¿Como funciona la seleccion?
```
Seleccionaremos los primeros 14 registros completos (con todos los campos
llenos y foto incluida). El orden de llegada determina los seleccionados.
```

**Pregunta 2:** ¿Que pasa si el cupo ya esta lleno?
```
Si ya se completaron los 14 lugares, tu registro quedara en lista de espera
para la siguiente edicion de la revista.
```

**Pregunta 3:** ¿Que requisitos debe cumplir mi foto?
```
- Formato: JPG o PNG
- Peso maximo: 10 MB
- Recomendacion: cuadrada (1:1), minimo 1080x1080 pixeles
- Fondo limpio y profesional
- Debe ser una foto tuya (no logos ni graficos)
```

**Pregunta 4:** ¿Mis datos estaran seguros?
```
Si. Tu informacion sera utilizada unicamente para fines editoriales de AMPI
Riviera Nayarit. Consulta nuestro aviso de privacidad para mas detalles.
```

---

### G) SECCION CTA FINAL (lineas 522-541)

**Texto nuevo:**
```
"¡No te quedes fuera! Postulate ahora"
```

**Boton:**
```
"Completar mi postulacion" → enlaza a #form
```

---

## 4. Mensajes de SweetAlert2 (JavaScript)

Ubicacion: dentro del `<script>` al final de index.html (lineas ~600+)

**Mensaje de exito:**
```javascript
Swal.fire({
  icon: 'success',
  title: '¡Listo!',
  text: 'Recibimos tu postulacion. Te contactaremos si tu perfil queda seleccionado.',
  confirmButtonColor: '#00de81'
});
```

**Mensaje de error/campos faltantes:**
```javascript
Swal.fire({
  icon: 'warning',
  title: 'Informacion incompleta',
  text: 'Para completar tu postulacion, agrega los campos marcados con *',
  confirmButtonColor: '#241442'
});
```

**Mensaje de cupo lleno (OPCIONAL - si se implementa):**
```javascript
Swal.fire({
  icon: 'info',
  title: 'Cupo completo',
  text: 'Gracias por tu interes. El cupo de esta edicion se ha completado. Puedes registrarte a lista de espera para la siguiente.',
  confirmButtonColor: '#241442'
});
```

---

## 5. Estilos CSS Adicionales Sugeridos

Agregar en `assets/css/style.css`:

```css
/* Aviso de cupo limitado */
.cupo-limitado {
  background: rgba(248, 188, 36, 0.15);
  border-left: 4px solid #F8BC24;
  padding: 15px 20px;
  margin: 20px 0;
  border-radius: 0 10px 10px 0;
}

/* Grupos de checkboxes */
.checkbox-group {
  display: flex;
  flex-wrap: wrap;
  gap: 15px;
  margin-top: 10px;
}

.checkbox-group label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

/* Checkbox individual (consentimientos) */
.checkbox-single label {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  line-height: 1.4;
}

.checkbox-single input[type="checkbox"] {
  margin-top: 4px;
}

/* Texto bajo boton enviar */
.texto-envio {
  font-size: 12px;
  color: #666;
  text-align: center;
  margin-top: 15px;
}

/* Input de archivo (foto) */
input[type="file"] {
  padding: 10px;
  border: 2px dashed #ccc;
  border-radius: 10px;
  width: 100%;
  cursor: pointer;
}

input[type="file"]:hover {
  border-color: #00de81;
}

/* Seccion de nota de validacion */
.nota-validacion {
  background: #f5f5f5;
  padding: 15px;
  border-radius: 10px;
  font-size: 14px;
  margin-top: 20px;
}

/* Titulos de seccion dentro del form */
form h4 {
  color: #241442;
  margin: 25px 0 15px 0;
  padding-bottom: 10px;
  border-bottom: 2px solid #00de81;
}
```

---

## 6. Imagenes a Reemplazar/Agregar

| Imagen actual | Usar para | Notas |
|---------------|-----------|-------|
| `banner-2-img-1.jpg` | Banner principal | Imagen de agente o revista |
| `banner-2-img-2.jpg` | Banner secundaria | Imagen profesional |
| `about-2-image-1.png` | Seccion About | Portada revista o grupo agentes |
| `about-2-image-2.png` | Seccion About | Imagen complementaria |
| `faq-2-img-1.jpg` | Seccion FAQ | Puede mantenerse o cambiar |
| `cta-2-img.png` | CTA final | Imagen motivacional |

**Formato recomendado:** JPG para fotos, PNG para graficos con transparencia

---

## 7. Checklist de Entrega

- [ ] Banner actualizado con nuevos copys
- [ ] Formulario completo con todos los campos
- [ ] Seccion informativa (lado izquierdo del form)
- [ ] 3 tarjetas de beneficios actualizadas
- [ ] Seccion About con nuevo contenido
- [ ] Estadisticas actualizadas
- [ ] FAQ con nuevas preguntas
- [ ] CTA final actualizado
- [ ] Estilos CSS adicionales agregados
- [ ] Imagenes reemplazadas (si se proporcionan)
- [ ] Mensajes de SweetAlert actualizados
- [ ] Responsive funcionando en mobile
- [ ] Header y Footer intactos

---

## 8. Notas Importantes

1. **NO modificar** la URL del Google Apps Script (eso lo configura el equipo interno)
2. **NO modificar** el Google Analytics ID
3. **Mantener** todas las clases de Tailwind existentes para consistencia
4. **Probar** en mobile antes de entregar (usar Chrome DevTools)
5. El campo `form_id` oculto debe ser: `postulacion_14_agentes`

---

## 9. Contacto para Dudas

Si tienes dudas sobre el proyecto, comunicate con el equipo de AMPI Riviera Nayarit.

---

*Documento generado el 21 de enero de 2026*
