# Recetas

Recetario compartido para dos, en la línea de Crouton, pero como app web. Funciona en iPhone y Android con la misma dirección y se instala en la pantalla de inicio. Comparte proyecto de Firebase con la lista de la compra y el calendario.

## Qué hace

- **Biblioteca** de recetas con foto, búsqueda por nombre, ingrediente o etiqueta, favoritas y orden por recientes, A–Z, más cocinadas o más rápidas.
- **Importar** desde una web (lee el formato estándar de recetas que usan casi todas las webs de cocina), desde un texto pegado o desde una foto de un libro (lee el texto de la imagen en el propio móvil).
- **Raciones**: cambia las raciones y se recalculan las cantidades, con fracciones (½, ¼, ⅓…).
- **Modo cocina**: un paso por pantalla en letra grande, se pasa con los botones o deslizando, la pantalla no se apaga y bajo cada paso aparecen las cantidades de los ingredientes que menciona.
- **Temporizadores**: los tiempos de los pasos («10 minutos», «media hora») se pueden tocar para lanzar un temporizador. Admite varios a la vez, con pausa, pitido y vibración.
- **A la compra**: propone los ingredientes con la cantidad según las raciones, y solo se añaden los que se marquen. La sección se toma del producto si ya existe en la lista, o se adivina.
- **Al menú**: pone la receta en la comida o la cena de un día del calendario. Si no existe el plato, lo crea con sus ingredientes.
- **Compartir** una receta como texto (WhatsApp, Notas…) y **exportar** todas en JSON como copia de seguridad.

## Coste

| Pieza | Servicio | Coste |
|---|---|---|
| Alojamiento | GitHub Pages | Gratis |
| Base de datos y fotos | Firebase Firestore (plan Spark) | Gratis |
| Inicio de sesión | Firebase Authentication | Gratis |
| Importar desde webs | Cloudflare Workers (opcional) | Gratis, 100.000 peticiones al día |
| Leer fotos de recetas | Tesseract.js, en el propio móvil | Gratis |

Las fotos se guardan en Firestore y no en Firebase Storage porque Storage ahora exige el plan Blaze con tarjeta. Cada foto se reduce a unos 100–200 KB; el Gigabyte gratuito de Firestore da para miles.

## Archivos

| Archivo | Para qué sirve |
|---|---|
| `index.html` | La app completa. |
| `config.js` | El mismo de las otras dos apps (no está en este paquete). |
| `sw.js` | Permite abrir la app sin conexión. |
| `manifest.webmanifest` e iconos | Instalación en la pantalla de inicio. En Android, además, la app aparece en «Compartir». |
| `firestore.rules` | Copia de referencia de las reglas completas. |
| `worker.js` | Intermediario opcional para importar desde webs (ver abajo). |

## Modelo de datos

**`recetas`**: `titulo`, `raciones`, `prep` y `coccion` (minutos), `ingredientes` y `pasos` (listas de líneas; las que empiezan por `#` son apartados), `etiquetas`, `notas`, `fuente`, `fuenteNombre`, `favorita`, `vecesCocinada`, `ultimaVez`, `miniatura` (foto pequeña), `tieneFoto`, `fotoUrl` (foto externa si no se pudo copiar), `creadoPor`, `creadoPorNombre`, `creadoEn`, `editadoPor`, `editadoEn`.

**`fotos`**: un documento por receta con el mismo identificador y el campo `data` (foto grande). Solo se descarga al abrir la receta.

**Colecciones de las otras apps**: añade productos a `items` con el mismo formato que la lista de la compra, crea platos en `platos` (con el campo extra `recetaId`, que el calendario ignora) y escribe en `menu`.

## Importar desde webs: por qué hace falta un intermediario

Por seguridad, un navegador no deja que una web lea otra directamente. La app prueba primero con vuestro intermediario (si está configurado) y, si no, con dos intermediarios públicos gratuitos, que funcionan pero a veces fallan o van lentos. Montar el vuestro en Cloudflare cuesta 5 minutos, no pide tarjeta y solo responde a vuestra app.
