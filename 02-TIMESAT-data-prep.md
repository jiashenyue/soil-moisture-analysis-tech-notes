# Tech Note 2: Preparing data for TIMESAT analysis

Shenyue Jia | 05/23/2024

## Overview

- This tutorial focuses on a 1-km downscaled SMAP soil moisture data product
  - SMAP-Derived 1-km Downscaled Surface Soil Moisture Product, Version 1
  - [More information](https://nsidc.org/data/nsidc-0779/versions/1)
- The original GeoTIFF images of this product needs further process to be utilized in TIMESAT
  - Separating ascending and descending overpass as individual files
  - Converting all GeoTIFF images to BIL (binary format) to be used in TIMESAT

## About ascending and descending overpass of SMAP
- Each GeoTIFF image from this data product contains **two bands**, representing the ascending and descending overpass of SMAP
  - Ascending (AM): occurs at 0600 hrs local time 
  - Descending (PM): occurs at 1800 hrs local time 
  - [More inforation about ascending and descending scanning of SMAP](https://smap.jpl.nasa.gov/data/)

**Two bands**
![img]()

**Ascending overpass**
![img]()

**Descending overpass**
![img]()

**Ascending overpass coverage of California**
![img]()

**Descending overpass coverage of California**
![img]()

## Separating individual bands

- Keep using a consistent collection (ascending or descending)
  - The same location does not get daily coverage
- After downloading and subsetting all 1-km SMAP product, loop over all subsetted images to separate **Band 2** from the original image if using descending images

## Converting TIF to a binary image (BIL)

- TIMESAT can only read a binary image
  - TIF (GeoTIFF) is NOT a binary image since the original data has been compressed
- A binary image is a straightforward matrix with X and Y as Columns and Rows
  - TIMESAT needs this information (columns and rows) and data type of values stored in this matrix to read the data

**An example of binary image matrix**  
![img]()

- BIL (.bil) format is a widely used binary image format
  - BIL means “band interleaved by line”
  - [More information about BIL and other formats of binary image](https://desktop.arcgis.com/en/arcmap/latest/manage-data/raster-and-images/bil-bip-and-bsq-raster-files.htm)

## TIMESAT installation
- Follow the tutorial of TIMESAT 3.3 in [this link](https://web.nateko.lu.se/timesat/docs/TIMESAT33_SoftwareManual.pdf)
  - Make sure you download the TIMESAT standalone version 3.3 for Windows users without Matlab
    -[Download link](https://web.nateko.lu.se/timesat/timesat.asp?cat=4)

## An example of reading a BIL image in TIMESAT

- Open TIMESAT menu system and click TSM image view

![img]()

- Go to File/Open Image File to identify a binary (.bil) you have saved

![img]()

- You also need the number of rows and columns per image information
  - If using ArcGIS Pro, you can get the information from layer properties

![img]()

- If all parameters are correctly added, the image will show up like this
  - Adjusting the minimal value of pixel can help you see more details of the subtle changes
  - The default minimal value is sometimes too small to let the details show up

![img]()

## Reference
- [A tutorial of time series analysis using TIMESAT](https://datapartnership.org/syria-economic-monitor/notebooks/vegetation-conditions/Seasonality_Parameters_Data_Extraction.html)
