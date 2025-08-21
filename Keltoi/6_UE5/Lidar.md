# Shortcut for cmd
cd "C:\Program Files\QGIS 3.36.3\bin\"

# Pania della croce

gdal_translate -a_nodata 0 –of ENVI –ot UInt16 –scale –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/21e06_1x1_dtm_006_2008_3003_6082ca49ca4c0106c6a9cb80188f1822/21e06_1x1_dtm_006_2008_3003.asc" "G:/D/Produzione Videoludica/Pania della Croce/Prova.raw"

gdal_translate –of PNG –ot UInt16 –scale –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/prova.tif" "G:/D/Produzione Videoludica/Pania della Croce/Prova.png"

gdal_translate –of ENVI -a_nodata -9999.0 –ot UInt16 –scale –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/prova.tif" "G:/D/Produzione Videoludica/Pania della Croce/Prova.raw"

gdal_translate –of ENVI –ot UInt16 –scale –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/Prova.tif" "G:/D/Produzione Videoludica/Pania della Croce/Prova.raw"

gdalwarp -srcnodata "-9999" -dstnodata "0" "G:/D/Produzione Videoludica/Pania della Croce/21e06_1x1_dtm_006_2008_3003.tif" "G:/D/Produzione Videoludica/Pania della Croce/prova.tif"

gdalwarp -srcnodata "-9999" -dstnodata "0" "G:/D/Produzione Videoludica/Pania della Croce/21e06_1x1_dtm_006_2008_3003_6082ca49ca4c0106c6a9cb80188f1822/21e06_1x1_dtm_006_2008_3003.asc" "G:/D/Produzione Videoludica/Pania della Croce/prova.asc"

gdal_translate –of ENVI –ot UInt16 –scale –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/prova.asc" "G:/D/Produzione Videoludica/Pania della Croce/prova.raw"


gdal_translate -a_srs EPSG:3003 -of PNG –ot UInt16 –scale –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/prova.asc" "G:/D/Produzione Videoludica/Pania della Croce/Prova.png"

gdal_translate -a_srs EPSG:3003 -of GTiff –ot UInt16 –scale –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/prova.asc" "G:/D/Produzione Videoludica/Pania della Croce/Prova.tif"

gdal_translate -a_srs EPSG:3003 -of GTiff –ot UInt16 –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/prova.asc" "G:/D/Produzione Videoludica/Pania della Croce/Prova_noscale.tif"

gdal_translate -a_srs EPSG:3003 –of ENVI –ot UInt16 –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/prova.asc" "G:/D/Produzione Videoludica/Pania della Croce/prova.raw"

gdal_translate -a_srs EPSG:3003 –of ENVI –outsize 4033 4033 "G:/D/Produzione Videoludica/Pania della Croce/prova.asc" "G:/D/Produzione Videoludica/Pania della Croce/prova_noscale.raw"

# Lake District

gdal_translate –of ENVI –ot UInt16 –scale –outsize 4033 4033 "G:\D\Produzione Videoludica\Lake District\nodata.tif" "G:\D\Produzione Videoludica\Pania della Croce\nodata.raw"

gdalwarp -srcnodata "-9999" -dstnodata "0" "G:\D\Produzione Videoludica\Lake District\NY30nw_DTM_1m.tif" "G:\D\Produzione Videoludica\Lake District\nodata.tif"

# Ebridi

For the scale > https://forums.unrealengine.com/t/heightmap-vertice-values-to-world-height/112388/10

-scale MinLidarHeight MaxLidarHeight MinLandscapeHeight MaxLandscapeHeight

gdal_translate -of PNG -ot UInt16 -scale -0.979 161.752 0 65535 –outsize 4033 4033 -a_nodata -9999.0 "G:/D/Produzione Videoludica/Heightmap/Ebridi/NG49NW_50CM_DTM_OUTERHEBRIDES2019.tif" "G:/D/Produzione Videoludica/Heightmap/Ebridi/exports/Ebridi3.png"

gdal_translate -of PNG -ot UInt16 -scale -0.979 161.752 0 65535 "G:/D/Produzione Videoludica/Heightmap/Ebridi/NG49NW_50CM_DTM_OUTERHEBRIDES2019.tif" "G:/D/Produzione Videoludica/Heightmap/Ebridi/exports/Ebridi3.png"

gdalwarp -srcnodata "-9999" -dstnodata "0" "G:/D/Produzione Videoludica/Heightmap/Ebridi/exports/Ebridi3.png" "G:/D/Produzione Videoludica/Heightmap/Ebridi/exports/nodata.png"

To open adf files https://gis.stackexchange.com/questions/302870/importing-adf-files-in-arcmap

# Crater Lake

gdal_translate -of GTiff -ot UInt16 "G:/Produzione Videoludica/Heightmap/USA/Crater Lake/DS716-CraterLake_LiDAR/DS716-CraterLake_LiDAR/craterlake_1m/hdr.adf" "G:/Produzione Videoludica/Heightmap/USA/Crater Lake/DS716-CraterLake_LiDAR/DS716-CraterLake_LiDAR/craterlake_1m/prova.tif"

gdalwarp -srcnodata "-9999" -dstnodata "0" "G:/Produzione Videoludica/Heightmap/USA/Crater Lake/DS716-CraterLake_LiDAR/DS716-CraterLake_LiDAR/craterlake_1m/prova.tif" "G:/Produzione Videoludica/Heightmap/USA/Crater Lake/DS716-CraterLake_LiDAR/DS716-CraterLake_LiDAR/craterlake_1m/prova.tif"

gdal_translate -of PNG -ot UInt16 -a_nodata None -scale 1632 2051 0  65535 "G:/Produzione Videoludica/Heightmap/USA/Crater Lake/Tiles/hdr_07_04.tif" "G:/Produzione Videoludica/Heightmap/USA/Crater Lake/prova.png"

gdal_translate -of PNG -ot UInt16 -a_nodata None -scale 1292 2718 0  65535 "G:/Produzione Videoludica/Heightmap/USA/Crater Lake/FillData.tif" "G:/Produzione Videoludica/Heightmap/USA/Crater Lake/prova.png"

530,859375

```
gdalwarp -dstalpha
```

# Snowdon

gdalwarp -srcnodata "-9999" -dstnodata "0" "G:/Produzione Videoludica/Heightmap/UK/Wales/wg_del_11_261354_20210302dtm.tif" "G:/Produzione Videoludica/Heightmap/UK/Wales/wg_del_11_261354_20210302dtm_nodata.tif"

gdal_translate -of PNG -ot UInt16 -a_nodata None -scale 470 1077 0  65535 "G:/Produzione Videoludica/Heightmap/UK/Wales/wg_del_11_261354_20210302dtm_nodata.tif" "G:/Produzione Videoludica/Heightmap/UK/Wales/wg_del_11_261354_20210302dtm_nodata_scale.tif"

gdal_translate -of PNG -ot UInt16 -a_nodata None -scale 470 1077 0  65535 "G:/Produzione Videoludica/Heightmap/UK/Wales/wg_del_11_261354_20210302dtm_nodata.tif" "G:/Produzione Videoludica/Heightmap/UK/Wales/wg_del_11_261354_20210302dtm_nodata.png"

# Scale in UE5

BTW to get your landscape to the exact scale/proportions as in Gaea do this ... Get your landscape width and height, then you can perform the math within the landscape scale values. For instance, I know mine is 5.12 x 5.12 km and a height of 2,6km. So in the X and Y landscape scaling, type "100 * 5120/4033" where "4033" is replaced with your overall resolution. For Z, type "100 * 2600/512" where 2600 is my height in km and a constant of 512.

Learning a lot from you! I found building a normalized map in Gaea greatly increase the landscape's vertices resolution and keeps artifacts at a minimum. A raw build adds useless height data to your heightmap. A normalized build however uses the highest point in your landscape for a value of "1" being the brightest pixel and "0" being sea level. Then scale X and Y with "100 * width/resolution" and Z scale "100 * actualHeight/512". Then, to place sea level on the grid origin set Z location to "100 * actualHeight/2". This scales and positions the landscape with absolute accuracy.

100 * 5000/1009
100 * 2600/512

or

actualheight/height_scale_ratio*(height/actualheight) * 100 * 1/512

500/0.4*(2000/500)

2600/0.52*100/512

5000 * 100/512 - this is what you get
100 * height/height_scale_ratio/512

https://dev.epicgames.com/documentation/en-us/unreal-engine/importing-and-exporting-landscape-heightmaps-in-unreal-engine?application_version=5.4

https://dev.epicgames.com/documentation/en-us/unreal-engine/landscape-technical-guide-in-unreal-engine

# How to convert .las/.laz files in Qgis

https://forums.dovetailgames.com/threads/laz-las-file-conversion-tutorial.76688/

https://forums.dovetailgames.com/threads/lidar-tutorial.74374/

https://docs.google.com/document/d/e/2PACX-1vRfXOAa9n9ElXv6_3kGbaJ-ckwamCm6tzU9bKUxo8P6djUyCb1jPX13D1AbdYm3PapxTof-Q-7RdG3C/pub

# Split lidar in tiles

https://www.youtube.com/watch?v=cYfpIUVEYgU

# If the terrain is completely black

https://gis.stackexchange.com/questions/404103/converting-geotiff-to-png-results-in-completely-black-image

# Flux

ModifierContainerActor
ModifierSource > Waterfall itself
ModifierSourceActor > river down the waterfall

# Alpha brushes
https://dev.epicgames.com/community/learning/tutorials/m27q/unreal-engine-using-alpha-brushes-to-sculpt-your-landscape

gdal_translate -a_srs EPSG:3003 -a_nodata -9999.0 -ot UInt16 -of GTiff -scale 914.459 1283.592 0 65535 "G:/Produzione Videoludica/Heightmap/Italia/Toscana/Chiusi della verna/18o31_1x1_dtm_006_2008_3003_f1f470ece9142a0d95f984441b1a4e1d/18o31_1x1_dtm_006_2008_3003.asc" "G:/Produzione Videoludica/Heightmap/Italia/Toscana/Chiusi della verna/Verna.tif"