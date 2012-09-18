# Fits on Moon #

Jerome Gasperi, CNES - 2012.09.17

# Conversion fits -> tif (GDAL) #
> gdal_translate data/test.fits output/test.tif

# Adaptation de la dynamique (ImageMagick) #
> convert output/test.tif -normalize -format TIFF output/test_normalize.tif

# Récupération des points de controles (GDAL) #
Les points de controle (GCP) permettent d'associer une coordonnée image à son équivalent en Latitude/Longitude. Pour reprojeter l'image, on utilise les 4 coins de l'image. Les valeurs des Lat/Lon sont récuppérés dans l'entête FITS par gdalinfo

> gdalinfo data/test.fits

    Metadata:
      LONG0=-52.0200
      LAT0=-16.2480
      LONG1=-51.7630
      LAT1=-13.0030
      LONG2=-49.7350
      LAT2=-13.2590
      LONG3=-49.9690
      LAT3=-16.4510
      BZERO=32768
    Corner Coordinates:
      Upper Left  (    0.0,    0.0)
      Lower Left  (    0.0,  256.0)
      Upper Right (  512.0,    0.0)
      Lower Right (  512.0,  256.0)
      Center      (  256.0,  128.0)

# Reprojection de l'image (GDAL) #
L'association coins lon/lat est la suivante :

    UL = Upper Left
    UR = Upper Right
    LL = Lower Left
    LR = Lower Right

     UL = 3             UR = 0
        +------------------+
        |                  |
        |                  |
        |                  |
        |                  |
        |                  |
        |                  |
        |                  |
        +------------------+
     LL = 2             LR = 1

> gdal_translate -gcp 0.0 256.0 -49.7350 -13.2590  -gcp 0.0 0.0 -49.9690 -16.4510 -gcp 512.0 0.0 -52.0200 -16.2480 -gcp 512.0 256.0 -51.7630 -13.0030 output/test_normalize.tif output/test_withgcp.tif

> gdalwarp -of GTiff -t_srs EPSG:4326 output/test_withgcp.tif output/test_projected.tif

# Creation du WMS #
Voir le fichier moon.map

# Creation du fichier de métadonnées GeoJSON #
Voir le fichier output/test.json


