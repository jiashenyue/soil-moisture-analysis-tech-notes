# Tech Note 5: Working in TIMESAT

Shenyue Jia | 06/21/2024

## Overview

- This tech note document describes 
  - Workflow in TIMESAT
  - How to calculate seasonality metrics using TIMESAT

## Workflow in TIMESAT

1. Use `TSM_GUI` tool to setup parameters for time series analysis
  - Type of filter
  - Forced minimal value
  - Method to treat missing values, etc.
2. Save time series processing parameters in Step 1 as a `.set` file
3. Use `TSF_process` or `TSF_process parallel` to load the `.set` file saved in Step 2 and run a designated portion of the image
4. Use post-processing tools such as `TSM_printseasons` to convert Matlab files (outputs) into readable format of results (e.g., `.txt` files)

## Use `TSM_GUI` tool to setup parameters

- Open `TSM_GUI` tool in TIMESAT
- Open File/Load Image Files to select a `.txt` file with the following info to load images
  - Number of images (e.g., 730)
  - Path and file names of all images we want to process
- Specify input data, for example

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/061-tech-note.png)

- Click **Show image** to specify a window for data processing
- Click Processing window to enable a haircross cursor to select the upper left and lower right point of window for processing
  - **Select an area with data**
  - Click return
  - **This step needs at least two years of data to work out**
  
![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/062-tech-note.png)

  - Otherwise you will have a large number of coordinates with missing data
    - This is NOT an error, but does not help us select the correct parameters for time series processing efficiently

- An example showing a processing window covering the entire image (with a lot of missing data)
  - Row 6 to 965
  - Col 6 to 993

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/063-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/064-tech-note.png)

  - Load data, we get a warning - too many missing data points or constant data for Row 6, Column 6

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/065-tech-note.png)
  
  - Remember, this is NOT an error, but will take us much longer to find the first row and col with a decent time series to start our parameter setup

- An example showing a smaller processing window with much less no data pixels

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/066-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/067-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/068-tech-note.png)
  
- This is a sample time series in our data and we will determine how to process the time series for **all the other pixels** in our image
  - Data plotting: `Points` only
  - Fitting method: `Savitzky-Golay`
  - Data range: `0.01` to `1`
  - Spike method: `1: median filter`
  - Turn on Season start/stop
  - Turn on x-tick at year, Guidelines, and Legend
  - Keep the default settings for other parameters

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/069-tech-note.png)

- We can also find the Seasonality results from S-G fit for the time series at row `599`, col `405`
  - Season Start t = Day `324.9`
  - Season End t = Day `464.0`
  - Season Peak t = Day `399.9`

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/070-tech-note.png)

- We can check out a different pixel to see if these parameters work or not
  - Press **Plot next series** to move forward to the next pixel location
    - Row = `599`, **Col = `406`**

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/071-tech-note.png)

  - If we are satisfied with these parameter settings, we can save it as a `.set` file for all future processing

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/072-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/073-tech-note.png)

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/075-tech-note.png)

- It is possible to change the **Rows to process** and **Cols to process** to the entire image and save the setting file

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/074-tech-note.png)

## Processing images in `TSF_process` tool with settings file

- If we click TSF_process in TIMESAT menu, we will be asked to specify a `.set` file
- Then we will see a Fortran command window to let us provide some other parameters
- We can follow the directions shown in the command window to run the program with settings in the `.set` file
- In this case, we will use the `.set` file with a small processing window instead of the entire image for a test
  - Processing the entire image can take a lot time and `TSF_process parallel` is recommended

## Extract seasonality information after processing

- Click `TSM_printseasons` menu in TIMESAT
- Specify the `.tpa` file generated after processing
  - By default, TIMESAT saves outcomes to the working directory speccified in MATLAB

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/076-tech-note.png)

  - We can specify a different working directory in MATLAB
- The screenshot below shows the outcome of seasonality after finishing the small processing window specified in a `.set` file

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/077-tech-note.png)

- We can see the results are in the same format as we have seen in the `TSM_GUI`

## Importance of aligning SMAP and VIIRS data

- Since the outcome of seasonality analysis will be displayed by row and col indices, it is **extremely important** that our SMAP and VIIRS EVI data use the same pixel graticule, so that the outcome of phenological metrics, such as Start of Season and End of Season are comparable and can be used to compute the length of lag




