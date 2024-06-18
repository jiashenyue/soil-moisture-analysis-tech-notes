# Tech Note 2: Preparing data for TIMESAT analysis

Shenyue Jia | 05/23/2024

## Overview

-   This tutorial focuses on a 1-km downscaled SMAP soil moisture data product
    -   SMAP-Derived 1-km Downscaled Surface Soil Moisture Product, Version 1
    -   [More information](https://nsidc.org/data/nsidc-0779/versions/1)
-   The original GeoTIFF images of this product needs further process to be utilized in TIMESAT
    -   Separating ascending and descending overpass as individual files
    -   Converting all GeoTIFF images to BIL (binary format) to be used in TIMESAT

## About ascending and descending overpass of SMAP

-   Each GeoTIFF image from this data product contains **two bands**, representing the ascending and descending overpass of SMAP
    -   Ascending (AM): occurs at 0600 hrs local time
    -   Descending (PM): occurs at 1800 hrs local time
    -   [More inforation about ascending and descending scanning of SMAP](https://smap.jpl.nasa.gov/data/)

**Two bands** ![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/004-tech-note.png)

**Ascending overpass** ![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/005-tech-note.png)

**Descending overpass** ![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/006-tech-note.png)

**Ascending overpass coverage of California** ![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/008-tech-note.png)

**Descending overpass coverage of California** ![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/007-tech-note.png)

## Separating individual bands

-   Keep using a consistent collection (ascending or descending)
    -   The same location does not get daily coverage
-   After downloading and subsetting all 1-km SMAP products, loop over all subsetted images to separate **Band 2** from the original image if using descending images

## Converting TIF to a binary image (BIL)

-   TIMESAT can only read a binary image
    -   TIF (GeoTIFF) is NOT a binary image since the original data has been compressed
-   A binary image is a straightforward matrix with X and Y as Columns and Rows
    -   TIMESAT needs this information (columns and rows) and data type of values stored in this matrix to read the data

**An example of binary image matrix**\
![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/009-tech-note.png)

-   BIL (.bil) format is a widely used binary image format
    -   BIL means "band interleaved by line"
    -   [More information about BIL and other formats of binary image](https://desktop.arcgis.com/en/arcmap/latest/manage-data/raster-and-images/bil-bip-and-bsq-raster-files.htm)

## TIMESAT installation

### Download TIMESAT

-   Follow the tutorial of TIMESAT 3.3 in [this link](https://web.nateko.lu.se/timesat/docs/TIMESAT33_SoftwareManual.pdf)
    -   Make sure you download the TIMESAT version 3.3 for Windows users **with** Matlab installed
        -   [Download link](https://web.nateko.lu.se/timesat/timesat.asp?cat=4)

### Matlab free trial installation

-   As students or academia users, we can download a free trial version of Matlab
    -   [Download link](https://www.mathworks.com/products/matlab/student.html)
    -   This requires your \*.edu email address and account registration with this email address
    -   After registrating an account with Matlab, you can find the download options for different versions of Matlab
-   I downloaded Matlab 2016b and installed this version just in case there is anything wrong with the latest version
    -   This installation may take some time as I recommend installing all the libraries that come with the software
    -   Also download and install the updates for Matlab 2016b

### Configure paths in Matlab to run TIMESAT

-   We need to add TIMESAT installation folder in Matlab so that the program can run
    -   You can also find the same directions in [TIMESAT 3.3 Software Manual](https://web.nateko.lu.se/personal/Lars.Eklundh/TIMESAT/prog/version33/TIMESAT33_SoftwareManual.pdf), **page 47**

1.  Click *Set Path* menu

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/014-tech-note.png)

2.  Click *Add with Subfolders*

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/015-tech-note.png)

3.  Navigate to the folder **timesat_matlab** under the main **timesat33** folder
4.  *Save* and *Close*
5.  After this, set the TIMESAT working directory to \*\*timesat33\run\*\*

-   Click the Working Directory bar to navigate to your \*\*timesat33\run\*\* folder

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/016-tech-note.png)

6.  Type **TIMESAT** in Matlab Command Window to run the program

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/017-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/018-tech-note.png)

## An example of visualizing a BIL image in TIMESAT

-   After starting TIMESAT, click TSM image view

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/010-tech-note.png)

-   Go to File/Open Image File to identify a binary (.bil) you have saved

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/011-tech-note.png)

-   You also need the number of rows and columns per image information
    -   If using ArcGIS Pro, you can get the information from layer properties
    -   **Updated on 5/30/24: If using a BIL subset by your Python script with `arcpy`, please use the following version of rows and cols as this is slightly different from the old numbers I provided**
        - Number of rows: 966
        - Number of cols: 998

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/013-tech-note.png)

-   If all parameters are correctly added, the image will show up like this
    -   **Adjusting the minimal value of pixel can help you see more details of the subtle changes**
    -   The default minimal value is sometimes too small to let the details show up

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/012-tech-note.png)

## Read a list of BIL images for time series analysis

### Obtain a `txt` file with only `.bil` files in a directory

- Open Windows PowerShell
  - Press `Win` + `S` to open the search bar, type "PowerShell", and select "Windows PowerShell" or "Windows PowerShell (Admin)" if you need administrative privileges.
  - Or just search for `PowerShell` to run this program
- Run the script below

##
        # Define the directory you want to search
        $directory = "C:\path\to\your\directory"
        # Define the output text file path
        $outputFile = "C:\path\to\your\output.txt"
        # Get the full paths of all .bil files in the directory
        Get-ChildItem -Path $directory -Filter *.bil -Recurse | Select-Object -ExpandProperty FullName | Out-File -FilePath $outputFile -Encoding UTF8

- Replace `C:\path\to\your\directory` with the actual path to the directory where your .bil files are located. Also, replace `C:\path\to\your\output.txt` with the desired output file path and name.
  - See an example below
  
![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/019-tech-note.png)

- File list `txt` file

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/020-tech-note.png)

- For some reason, the above command still produced a `txt` file encoded with **UTF-8 BOM**, this will cause trouble in the next step
- Save this file to a new file and change the encoding type to **UTF-8**

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/056-tech-note.png)

- Adding a line for the number of files included in this `txt` file so that TIMESAT can read data correctly
  - For example, this sample file below contains **5** BIL files
  - We need to put **5** in the first line of the `txt` file so that TIMESAT knows how many files to load

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/053-tech-note.png)

- There are many ways to check the number of images included in the file list if the list is generated by a Windows PowerShell script
  - Opening the file in Microsoft Excel

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/054-tech-note.png)

  - Moving the mouse cursor to the last line of `txt` file and check the `Ln` number

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/060-tech-note.png)

### Open a list of image in TIMESAT

- Open this new version of file list file in TIMESAT TSM_imageview window

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/055-tech-note.png)

- Select a file and put correct file type and dimension info to load
  - If the first file does not load with an error, select another file to draw with the dimension info and data type info
  
![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/057-tech-note.png)

Error if selecting the first image to load

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/058-tech-note.png)

Select a second image and it draws correctly

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/059-tech-note.png)

Switch back to the first image and it draws correctly

### Tips for troubleshooting

- Check the format and content of file in `..\timesat33\data\`
  - This is a folder with sample data to run the tutorial in TIMESAT Software Manual (see **Sec. 9 Getting started with TIMESAT - a quick tutorial**)
  - Directory `wa` under this folder saves a number of NDVI images in a consecutive time series (same as the format we will be working on)
  - You can check the content of `ndvilistwa.txt` under `..\data\wa\` to see the correct format of a file list that can be read by TIMESAT

- We can learn how to conduct further analysis step by step with the sample data and the tutorial before applying our own data to make sure we understand the meaning of these metrics


## Reference

-   [A tutorial of time series analysis using TIMESAT](https://datapartnership.org/syria-economic-monitor/notebooks/vegetation-conditions/Seasonality_Parameters_Data_Extraction.html)
-   [TIMESAT 3.3 Software Manual](https://web.nateko.lu.se/personal/Lars.Eklundh/TIMESAT/prog/version33/TIMESAT33_SoftwareManual.pdf)
