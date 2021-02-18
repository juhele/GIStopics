
### Dosimeter pickup plugin for QGIS

*Requirements*
- QGIS 3.x plugin
- use UTF-8 CR-LF encoding for text outputs for full Windows/Unix compatibility

*Input data*

**Positions of the pickup stations**
- real data published by the State Office for Nuclear Safety (SÚJB) in the "National monitoring program" (Národní program monitorování) documents - available here in Czech
https://www.sujb.cz/dokumenty-a-publikace/narodni-program-monitorovani
- the positions are from these tables:
www.sujb.cz/fileadmin/sujb/docs/dokumenty/NPM/010119/Priloha_c_1_NPM_TABULKY_RADY_B.pdf
- description of the territorial network of thermoluminescent dosemeters (TLD) here:
- English: https://www.suro.cz/en/rms/tld
- Czech: https://www.suro.cz/cz/rms/tld

Format:
OGC GeoPackage, WGS84 UTM33N coordinate system (EPSG:32633) + lat/lon (EPSG:4326) data in attribute table.

structure
```
fid;latitude;longitude;altitude_m;merici_misto
1;49.7775;14.693056;363;Benešov
2;49.7775;14.693056;363;Benešov b
3;49.963969;14.096098;225;Beroun
4;49.964407;14.095817;225;Beroun b
5;49.36875;16.643166;294;Blansko
6;49.432667;13.883028;441;Blatná
```

*Introduction*

The territorial network of thermoluminescent dosimeters (TLD) is a part of the Czech Radiation Monitoring Network. Consists of 180 points with dosimeters - some outdoors, some in buildings.

In case of a normal radiation situation, the measurements are performed quarterly, in case of a radiation accident more often. The dosimeters are gradually collected by car and transported to the lab to get the results.

details in English:
https://www.suro.cz/en/rms/tld

details in Czech:
https://www.suro.cz/cz/rms/tld/sit-termoluminiscencnich-dozimetru-tld-sit

*Problem to solve*

As described above, the dosimeters are placed in various locations (e.g. input dataset with static locations and ID / name). The dosimeters are replaced with "new" ones from time to time and in case of some emergency it is planned to perform such replacement more often (the time interval is not important for this task).

So someone takes a car, picks up all the dosimeters and takes them in a lab to be analysed to get the dose values for each one. 

However we do not know how big was the part of the dose obtained during the transport - which may not be negligible for emergency situations and is different for each of the dosimeters:

**Task 1 - determine car stop at the pickup station**
- measurement data contain GPS, dose rate values and time (mostly in GPS Epoch format)
- input layer of stations is static
- it is not always possible to park directly at the station, some detection algorithm needs to be implemented (it is possible to calculate speed or use station proximity...)

**Task 2 - calculate the dose**
- relatively easy ... dose in microSieverts is dose rate in microSieverts per hour multiplied by time in hours for particular interval (between two measurement points)
- the dose for each dosimeter starts counting after pickup and ends with delivery - e.g. the time is not equal for the dosimeters (see pic above) 
- result can be some vector point layer (Shapefile, GeoPackage) with the calculated and measured data - all the auxiliary values can also be stored in the attribute table

**Task 3 - generate text report**
- simple text file is enough and universal for mailing or other processing
- use UTF-8 CR-LF encoding for text outputs for full Windows/Unix compatibility
- use some predefined description + add values for particular dosimeters - the dosimeter could have just some number and information about pickup station like "Dosimeter no.1, pickup station: Kutná Hora" where the number is automatically generated and pickup station name taken from the static station layer

**Available data**

- we can provide dataset of the pickup points and several car drive tracks with some values

PS: some code from https://ctu-geoforall-lab.github.io/qgis-ground-radiation-monitoring-plugin/ might be useful for reuse here :-)
