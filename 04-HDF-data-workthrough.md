# Tech Note 4: Understanding HDF file with VIIRS data as an example

Shenyue Jia \| 06/04/2024

## Overview

-   This tutorial explains HDF file structure and the basic I/O of this data format
    -   We will use VIIRS Vegetation Indices (1km, 16-day, [VNP13A2 v002](https://lpdaac.usgs.gov/products/vnp13a2v002/)) as an example

## Decoding VIIRS file names

**Example: VNP13A2.A2014353.h08v04.002.2024063093840.h5**

-   `VNP13A21`: Data collection name
-   `A2014353`: Data acquisition date
    -   This file is data for the year 2014 and on day of year (DOY) 353
    -   [DOY calendar](https://gml.noaa.gov/grad/antuv/Calendar.jsp)
    -   DOY 353 in the year of 2014 is December 19, 2014

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/034-tech-note.png)

-   `h08v04`: Horizontal and vertical granule ID (VIIRS or MODIS tiling system)
    -   Find more information about [VIIRS griding system](https://lpdaac.usgs.gov/data/get-started-data/collection-overview/missions/s-npp-nasa-viirs-overview/#viirs-grid-systems)

![img](https://lpdaac.usgs.gov/media/images/modis_sinusoidal_grid.width-800.jpg)

-   `002`: Process version ID
    -   In this case, we are using the 2nd process version of this product
    -   There is a V001 product as well but it is recommended to use the latest version
-   `2024063093840`: Production year, Julian day, and time (YYYYDDDHHMMSS)
        -   Note that this is **different** from the data acquisition date (2014353)
-   `.h5`: File suffix of Hierarchical Data Format 5 (HDF-5)

## Understand Hierarchical Data Format (HDF)

-   HDF was designed to effectively store **multidimensional arrays**, especially for large data files

![img](https://upload.wikimedia.org/wikipedia/commons/6/60/HDF-Structure-Example.gif)

An image from [HDF Wikipedia Page](https://en.wikipedia.org/wiki/Hierarchical_Data_Format%20showing%20its%20structure)

-   Information of variables stored in this file is usually stored as a low dimensional array
    -   E.g., number of rows, columns, scaling factor of values of variables, etc.
-   Data of variables is usually stored as a two-dimensional or three-dimensional array
    -   Usually in the type of integer or long to save storage space
    -   Users can compute the actual value of variables with the information of *scaling factor* in many cases

## Check the structure of HDF with HDFViewer

-   An open source tool provided by the developer of HDF
    -   [Download link](https://www.hdfgroup.org/downloads/hdfview/)
    -   Requires Java to run
-   Download [HDFView for Win64](https://www.hdfgroup.org/downloads/hdfview/)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/044-tech-note.png)

- HDFView can help us get the information of variable names and scalars associated with 2-D variables, such as the scaling factor of a variable
  - Information of an example HDF-5 file shown in HDFView

### How to view HDF-5 file information from HDFView?

- After loading a `.h5` data into HDFView, we will see a directory like this

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/045-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/046-tech-note.png)

- In this example using VIIRS data, after expanding under `HDFEOS\GRIDS\VIIRS_Grid_16Day_VI_1km\Data Fields`, we will see a list of variables in their **long names**

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/047-tech-note.png)

- If click on `1 km 16 days EVI2`, we will see more information of variables

![img](![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/047-tech-note.png))

  - This table shows the `Name` of variables and `Type` of each
    - For example, `scale_factor` is a 64-bit floating point number
- If double-clicking on `scale_factor`, a new window shows up and this value indicates the scale factor is `1.0E-4`, or `0.0001`

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/048-tech-note.png)

- If double-clicking on `valid_range`, a new window shows the value range of this variable (EVI2)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/049-tech-note.png)


- Based on the information of `scale_factor` and `valid_range`, we can compute the actul valid value range is **-1 to 1**
  - -10000*0.0001
  - 10000*0.0001

- Single-click on `Projection`, we will find the following variables
  - If doubl-clicking each of them, we will find the values of these variables
  
![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/050-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/051-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/052-tech-note.png)

- The information is useful as we can take an example of HDF-5 we want to read and know where to read some key information if an I/O library did not correctly find the information

## Open HDF data in ArcGIS Pro

-   HDF will be treated as a **multidimensional** data type in ArcGIS Pro
    -   Refer to [Work with multidimensional raster data](https://pro.arcgis.com/en/pro-app/latest/help/data/imagery/working-with-a-multidimensional-raster-layer.htm#GUID-A4E386E5-266F-4CB0-9F93-A73A5AFC7AAB) for detailed steps to add HDF to an ArcGIS Pro project
-   Below is a subset of variables available in HDF files of VNP13A2 dataset

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/035-tech-note.png)

-   Select EVI2 and display this single variable in ArcGIS Pro

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/036-tech-note.png)

-   Check Layer Properties of this variable
  - 1200 cols and 1200 rows
  - 1 band
  - Cell size = 926.6 m
  - 16 bit integer data type
  
![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/037-tech-note.png)  
  
- Clearly this image is not shown as a square shape as indicated by the number of cols and rows. Why is that?
  - The 1200 * 1200 shape is created under a different projected reference system, which is **different** from the one we currently use in our ArcGIS Pro project
    - VIIRS and MODIS data uses a sinusoidal projection

### Spatial Reference of VIIRS and MODIS data

-   Below is the information of the VIIRS EVI2 variable we just loaded
    -   Spatial reference is an `unnamed` projected coordinate system but in `sinusoidal` type

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/038-tech-note.png)

-   VIIRS and MODIS data uses a **sinusoidal** projection like below
  - Unlike a standard sinusoidal projection, VIIRS and MODIS products use a unique  sphere measuring 6371007.181 meters 

![img](https://lpdaac.usgs.gov/media/images/modis_sinusoidal_grid.width-800.jpg)

  - If we zoom in to tile h08v05, we will find the tile covering most of California
    - This tile is a square with dimension of 1200 x 1200
  
![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/040-tech-note.png)

  - Tile h08v05 of different variables stored in VNP13A2 data

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/043-tech-note.png)

-   Projected coordinate reference system used by VIIRS and MODIS data

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/039-tech-note.png)

  - If we change the map coordinate system  for display to the Sinusoidal projection in ArcGIS Pro, we will see an image with a dimension of 1200 by 1200 (a square as shown in the tiling system)
    - See how the boundary of California is distorted as a result of projection change

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/042-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/043-tech-note.png)


## Reference

- [VIIRS Overview](https://lpdaac.usgs.gov/data/get-started-data/collection-overview/missions/s-npp-nasa-viirs-overview/#viirs-temporal-and-spatial-resolution)
