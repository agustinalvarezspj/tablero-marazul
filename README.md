# Tablero Operativo — Mar Azul Suites

Tablero semanal de tareas del equipo. Corre como página estática en GitHub Pages y guarda los datos en un Google Sheet a través de Google Apps Script.

- **App:** https://agustinalvarezspj.github.io/tablero-marazul/
- **Datos:** [Google Sheet del tablero](https://docs.google.com/spreadsheets/d/13opRk7UseFFHSKmj5VQ64uwYJrHWlMvT333wpawvs7g/edit)

## Qué hace

- Tareas semanales por área y por persona, con prioridad, fecha límite y notas.
- **Traslado automático:** toda tarea que quedó pendiente de una semana anterior pasa sola a la semana actual, con una etiqueta «↪ Arrastrada» que indica hace cuántas semanas viene arrastrándose. Ninguna tarea se pierde en el camino.
- **Mail diario:** cada persona recibe todos los días un correo con sus tareas pendientes (las vencidas marcadas en rojo).
- **App instalable (PWA):** desde el celular se puede agregar como ícono a la pantalla de inicio y usarla como una app.

## ⚠️ Instalación del backend (hacer una sola vez)

El traslado automático y el mail diario viven en el Google Apps Script. Hay que actualizarlo con el código de [`apps-script/Code.gs`](apps-script/Code.gs):

1. Abrí el [Google Sheet del tablero](https://docs.google.com/spreadsheets/d/13opRk7UseFFHSKmj5VQ64uwYJrHWlMvT333wpawvs7g/edit) → menú **Extensiones → Apps Script** (o entrá directo al proyecto en https://script.google.com).
2. Borrá el contenido de `Código.gs` y pegá el contenido completo de `apps-script/Code.gs`.
3. **Completá los mails** en la constante `EMAILS` (los que queden en `''` no reciben correo).
4. En **Configuración del proyecto** (engranaje) verificá que la zona horaria sea **Buenos Aires** (GMT-3), así el mail sale a la hora correcta.
5. Ejecutá una vez la función **`configurarDisparadorDiario`** (elegila en el menú desplegable de arriba y tocá ▶ Ejecutar). Aceptá los permisos que pide (leer la planilla y enviar mails). Eso deja programado el mail diario a las 8:00 (cambiable con `HORA_MAIL`).
6. **Importante — mantener la misma URL:** andá a **Implementar → Administrar implementaciones**, tocá el lápiz ✏️ de la implementación existente, en «Versión» elegí **Nueva versión** y guardá. Así la web sigue funcionando sin tocar nada. (Si en cambio creás una implementación nueva, la URL cambia y hay que actualizar `API_URL` en `index.html`.)

Para probar sin esperar: ejecutá `probarTrasladoAhora` (mueve los pendientes viejos a esta semana) o `probarEmailAhora` (manda el mail ya).

## Instalar la app en el celular

- **Android (Chrome):** abrir la app → botón verde «⬇ Instalar en el celular» en la pantalla de perfiles, o menú ⋮ → «Agregar a la pantalla principal».
- **iPhone (Safari):** abrir la app → botón Compartir (cuadrado con flecha) → «Agregar a pantalla de inicio».

Queda con el ícono azul «MA ✓» y abre a pantalla completa como una app.

## Estructura

| Archivo | Qué es |
|---|---|
| `index.html` | Toda la aplicación (HTML + CSS + JS) |
| `apps-script/Code.gs` | Backend para pegar en Google Apps Script |
| `manifest.webmanifest`, `sw.js`, `icon-*.png` | PWA (instalable + funciona con mala señal) |

## Cómo funciona el traslado automático

Cada vez que alguien abre el tablero (y antes de cada mail diario), el Apps Script revisa la planilla: toda fila con `done = FALSE` y `weekKey` anterior a la semana en curso se actualiza a la semana actual y se le suma en la columna `carried` la cantidad de semanas arrastradas. La fecha límite original se conserva, por eso las vencidas siguen mostrándose en rojo.
