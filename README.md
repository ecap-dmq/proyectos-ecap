# Mapa de proyectos de la Estación Científica Agua y Páramo

El mapa utiliza `proyectos_ecap.csv` como fuente única de los proyectos. `proyectos_ecap.xlsx` es la versión editable de la misma base: contiene las mismas columnas y registros, con filtros y formatos para facilitar su mantenimiento. Al exportar la hoja `Proyectos` como CSV y recargar la página, los puntos, contadores, filtros y fichas informativas se reconstruyen automáticamente.

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

## Buscar proyectos

El campo **Búsqueda por palabras** consulta simultáneamente título, autoría, línea prioritaria, tipo, estado y resumen. Al escribir dos o más caracteres se muestran debajo hasta ocho coincidencias ordenadas por relevancia, con el título, la autoría y un fragmento del resumen; las palabras coincidentes aparecen resaltadas. Al seleccionar un resultado, el mapa habilita sus filtros, acerca la vista al punto y abre su ficha lateral.

Al pasar el cursor sobre un punto aparece una vista breve con tipo, estado, título, línea prioritaria, autoría y año. Al hacer clic se abre una ficha fija a la derecha con esa información, el resumen, las coordenadas y el presupuesto. El texto de la ficha puede seleccionarse y el botón **Cerrar** la oculta.

## Actualizar la base con Excel

1. Descargar o hacer una copia de respaldo de `proyectos_ecap.xlsx` y `proyectos_ecap.csv`.
2. Abrir `proyectos_ecap.xlsx` y editar la única hoja, denominada `Proyectos`.
3. Mantener exactamente los diez encabezados y su orden. No agregar filas de título, celdas combinadas ni columnas auxiliares.
4. Agregar, eliminar o actualizar registros dentro de la tabla. Para un proyecto nuevo, completar como mínimo `latitud` y `longitud`; `presupuesto` puede quedar vacío cuando el dato no esté disponible.
5. Guardar primero el Excel para conservar la versión editable.
6. Con la hoja `Proyectos` activa, seleccionar **Archivo → Guardar como** y elegir **CSV UTF-8 delimitado por comas (.csv)**. Si Excel avisa que el formato solo conserva la hoja activa, confirmar.
7. Guardar el archivo con el nombre exacto `proyectos_ecap.csv` y reemplazar el CSV anterior en la raíz del repositorio.
8. Verificar que el CSV conserve 10 columnas, que las coordenadas sigan en sus columnas y que títulos o resúmenes con comas permanezcan entre comillas.
9. Probar la actualización en <http://localhost:8000> antes de publicarla. El mapa solicita una copia reciente del CSV para evitar datos almacenados en caché.

El Excel es el archivo maestro para futuras ediciones y el CSV es la copia utilizada por el mapa. Después de cada cambio deben guardarse y publicarse ambos archivos con el mismo contenido. Para retirar un proyecto del mapa se elimina su fila completa en el Excel y luego se vuelve a generar el CSV; para ocultarlo temporalmente es preferible conservar una copia de respaldo fuera del CSV publicado.

Las comas y saltos de línea dentro de títulos o resúmenes son válidos cuando Excel guarda correctamente esos campos entre comillas. Las tildes, la `ñ` y otros caracteres se conservan al usar CSV UTF-8.

## Columnas de la base

Los diez encabezados deben existir, aunque algunos valores individuales puedan quedar vacíos.

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
| `presupuesto` | Aporte o presupuesto del proyecto en USD; se muestra en la ficha lateral | Opcional; usar un número sin símbolo de moneda o dejar vacío |

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

## Capas cartográficas

El filtro muestra las capas en este orden: límites provinciales, cantonales y parroquiales; Ejes FONAG; Áreas de Conservación Hídrica; Quito urbano; cobertura de la tierra 2024; vías; y ríos. Las vías se representan en gris y los ríos en celeste brillante. El mapa base usa directamente las teselas públicas de OpenStreetMap y no requiere una clave de API.

Al abrir el mapa, las únicas capas cartográficas activas son **Ejes FONAG** y **Cobertura de la tierra 2024**; los puntos de los proyectos también se muestran completos. Todas las demás capas y los tres tipos de estaciones comienzan desactivados.

Las vías y los ríos publicados fueron recortados geométricamente con el límite de Ejes FONAG antes de transformarse a WGS 84 geográfico e incorporarse al HTML. La versión pública contiene únicamente las porciones interiores resultantes; no necesita descargar archivos SHP durante su uso.

La cobertura 2024 fue disuelta mediante su clasificación de nivel 2 y posteriormente recortada con Ejes FONAG. La capa publicada contiene una geometría por clase presente dentro del ámbito. Su flecha permite desplegar u ocultar la leyenda cromática; el páramo se representa en morado para distinguirlo con claridad.

## Estaciones

El apartado **Estaciones** permite activar de forma independiente las estaciones hidrológicas, meteorológicas y pluviométricas. Todas se muestran con símbolos triangulares y un color diferente por tipo. Al situar el cursor sobre una estación se muestran su nombre, tipo, código, altitud, estado y provincia cuando esos atributos están disponibles. La versión publicada contiene 60 estaciones activas transformadas a WGS 84.

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
- `proyectos_ecap.csv`: fuente pública que consume el mapa.
- `proyectos_ecap.xlsx`: archivo maestro editable; su hoja `Proyectos` replica la tabla publicada.
- `logo.png`: logotipo del encabezado.

El personal encargado de actualizar proyectos puede trabajar en la hoja `Proyectos` del Excel y exportarla como CSV. No necesita instalar herramientas adicionales ni ejecutar scripts.

## Actualizar la versión publicada en GitHub Pages

El repositorio público es [https://github.com/ecap-dmq/proyectos-ecap](https://github.com/ecap-dmq/proyectos-ecap). La solución es completamente estática y usa rutas relativas, por lo que no requiere servidor de aplicaciones ni base SQL.

Para publicar una actualización de la base:

1. Validar primero el CSV en la versión local.
2. Reemplazar `proyectos_ecap.csv` en la raíz del repositorio, sin cambiar su nombre.
3. Subir también `proyectos_ecap.xlsx` para mantener sincronizada la versión editable.
4. Confirmar ambos cambios mediante un commit en GitHub o con Git.
5. Esperar a que GitHub Pages termine el despliegue.
6. Abrir <https://github.com/ecap-dmq/proyectos-ecap/> y verificar el número de proyectos, los filtros y varios puntos.

La versión pública contiene información y coordenadas exactas; cualquier fila nueva debe contar con autorización institucional antes de incorporarse.
