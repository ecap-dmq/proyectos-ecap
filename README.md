# Mapa de proyectos de la Estación Científica Agua y Páramo

El mapa utiliza `base_datos_proyectos_investigacion.csv` como fuente única de los proyectos. Al reemplazar o actualizar ese CSV y recargar la página, los puntos, contadores, filtros y cuadros informativos se reconstruyen automáticamente.

La versión pública está disponible en **[https://ecap-dmq.github.io/proyectos-ecap/](https://ecap-dmq.github.io/proyectos-ecap/)**.

## Ejecutar el mapa localmente

No se debe abrir el HTML con una dirección que empiece por `file:///`, porque el navegador bloquea la lectura automática del CSV.

1. Abrir una terminal en esta carpeta (`investigacion`).
2. Ejecutar uno de estos comandos:

   ```powershell
   python -m http.server 8000
   ```

   En Windows también puede funcionar:

   ```powershell
   py -m http.server 8000
   ```

3. Mantener abierta la terminal mientras se usa el mapa.
4. Abrir en el navegador: <http://localhost:8000>
5. Para detener el servidor, volver a la terminal y presionar `Ctrl + C`.

El mapa necesita conexión a Internet para descargar Leaflet, Papa Parse y las teselas del mapa base.

## Actualizar la base con Excel

1. Hacer una copia de respaldo de `base_datos_proyectos_investigacion.csv`.
2. Abrir el CSV en Excel.
3. No modificar los nombres ni el orden de los encabezados.
4. Agregar, eliminar o actualizar registros.
5. Guardar mediante **Archivo → Guardar como → CSV UTF-8 delimitado por comas (.csv)**.
6. Conservar exactamente el nombre `base_datos_proyectos_investigacion.csv` y reemplazar el archivo anterior en esta carpeta.
7. Recargar <http://localhost:8000>. El mapa solicita una copia reciente del CSV para evitar que el navegador muestre una versión almacenada en caché.

Antes de guardar, cada proyecto nuevo debe tener una `latitud` y una `longitud` válidas. No se deben combinar celdas, agregar títulos por encima de los encabezados ni guardar el archivo como libro de Excel (`.xlsx`). Para retirar un proyecto del mapa se elimina su fila completa; para ocultarlo temporalmente es preferible conservar una copia de respaldo fuera del CSV publicado.

Las comas y saltos de línea dentro de títulos o resúmenes son válidos cuando Excel guarda correctamente esos campos entre comillas. Las tildes, la `ñ` y otros caracteres se conservan al usar CSV UTF-8.

## Columnas de la base

Los nueve encabezados deben existir, aunque algunos valores individuales puedan quedar vacíos.

| Columna | Uso | Regla por registro |
| --- | --- | --- |
| `titulo` | Nombre mostrado en el cuadro informativo | Opcional; si está vacío se muestra “Sin título” |
| `autores` | Autoría del proyecto | Opcional |
| `anio` | Año del proyecto o documento | Opcional |
| `Línea prioritaria de investigación ECAP` | Color del punto y filtro por línea prioritaria | Opcional; un valor nuevo crea automáticamente una opción de filtro |
| `tipo` | Filtro por tipo de archivo/proyecto | Opcional; un valor nuevo crea automáticamente una opción de filtro |
| `estado` | Filtro y contadores de proyectos en curso/finalizados | Opcional |
| `latitud` | Coordenada geográfica norte/sur en WGS84 | Obligatoria; número entre `-90` y `90` |
| `longitud` | Coordenada geográfica este/oeste en WGS84 | Obligatoria; número entre `-180` y `180` |
| `resumen` | Resumen mostrado al consultar el punto | Opcional |

Para las coordenadas se recomienda usar punto decimal, por ejemplo `-0.230391` y `-78.154659`. El lector también tolera una coma decimal cuando el valor está correctamente entre comillas dentro del CSV.

## Sistema de coordenadas de los puntos

Los puntos están almacenados en el sistema geográfico **WGS 84, EPSG:4326**, expresado en grados decimales:

- `latitud`: posición norte/sur, entre `-90` y `90`;
- `longitud`: posición este/oeste, entre `-180` y `180`;
- el orden en el CSV es `latitud`, luego `longitud`;
- en Ecuador, ambos valores suelen ser negativos para ubicaciones al sur del ecuador y al oeste de Greenwich;
- se usa punto como separador decimal y no se añaden símbolos de grados, minutos, segundos ni letras de hemisferio.

Cuando un documento declara coordenadas, la base conserva el valor exacto convertido a grados decimales si el original estaba en otro formato. No se deben desplazar ni redondear esos puntos para evitar coincidencias visuales: el mapa separa únicamente su representación en pantalla cuando dos registros comparten la misma coordenada, sin modificar el CSV.

Si una fuente entrega coordenadas proyectadas —por ejemplo UTM—, deben transformarse a **EPSG:4326** con el huso y datum indicados por el documento antes de incorporarlas. Si el datum o el huso no están claros, no se debe asumir una conversión.

## Validación automática

Al cargar la base, el mapa:

- ignora filas completamente vacías;
- descarta únicamente las filas cuya latitud o longitud estén vacías, no sean numéricas o estén fuera de rango;
- mantiene funcionando el resto de los registros;
- muestra en la consola del navegador el número de cada fila descartada y su motivo;
- muestra un mensaje visible si falta algún encabezado o si el CSV no puede leerse.

Para revisar avisos técnicos en Chrome o Edge, abrir las herramientas de desarrollo con `F12` y consultar la pestaña **Consola**.

## Archivos principales

- `index.html`: entrada compatible con GitHub Pages.
- `mapa_proyectos.html`: mapa institucional y lógica de lectura del CSV.
- `base_datos_proyectos_investigacion.csv`: fuente única de los proyectos.
- `logo.png`: logotipo del encabezado.

El personal encargado de actualizar proyectos solo necesita modificar el CSV; las capas espaciales ya están incorporadas en el HTML publicado.

## Actualizar la versión publicada en GitHub Pages

El repositorio público es [Nachorock73/ecap-mapa-proyectos](https://github.com/Nachorock73/ecap-mapa-proyectos). La solución es completamente estática y usa rutas relativas, por lo que no requiere servidor de aplicaciones ni base SQL.

Para publicar una actualización de la base:

1. Validar primero el CSV en la versión local.
2. Reemplazar `base_datos_proyectos_investigacion.csv` en la raíz del repositorio, sin cambiar su nombre.
3. Confirmar el cambio mediante un commit en GitHub o con Git.
4. Esperar a que GitHub Pages termine el despliegue.
5. Abrir <https://nachorock73.github.io/ecap-mapa-proyectos/> y verificar el número de proyectos, los filtros y varios puntos.

La versión pública contiene información y coordenadas exactas; cualquier fila nueva debe contar con autorización institucional antes de incorporarse.
