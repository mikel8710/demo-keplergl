# demo-keplergl
usando **keplergl** para hacer analsis geospacial de los estratos sociales del Perú. fuente INEI 2020
>[Kepler.gl](https://kepler.gl/)
> is a powerful open source geospatial analysis tool for large-scale data sets.

>La idea es visualizar los [Planos Estratificados de Lima Metropolitana](https://www.inei.gob.pe/media/MenuRecursivo/publicaciones_digitales/Est/Lib1744/libro.pdf), en **Kepler.gl**, agregar otras capas de datos con información interna y luego realizar los distintos análisis.

Afortunadamente alguien ya hizo estos mapas en formato shp [GeoGPSPeru](https://www.geogpsperu.com/2020/10/plano-de-estratos-de-ingresos-2020-lima.html):clap: :raised_hands:, con ello solo falta pasarlo a un formato que Kepler.gl entienda (.csv or .geojson) para ello usamos [qgis](https://qgis.org/es/site/index.html) (probablemente exista otra forma más eficiente, pero tenía instalado qgis asi que evité buscar otra forma), una vez que se tiene el geojson solo queda subir el archivo y visualizar el mapa. 
