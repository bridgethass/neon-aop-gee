# Explore AOP Hyperspectral Data at the Santa Rita Experimental Range (SRER)

---

Author: Bridget Hass

Contributors: John Musinsky, Tristan Goulden, Lukas Straube

Last Updated: March 31, 2022

Objectives
---
- Introduce the Google Earth Engine (GEE) code editor 
- Read in different AOP raster data sets at the NEON site [SRER](https://www.neonscience.org/field-sites/srer)
- Read in multiple years of data and explore inter-annual differences
- Explore GEE visualization and export options

Requirements
---
-	A gmail (@gmail.com) and Earth Engine accounts (https://earthengine.google.com/new_signup/)
-	A basic understanding of the GEE code editor and the GEE JavaScript API. If you have never used GEE before, we recommend starting on the [google developers earth-engine page](https://developers.google.com/earth-engine/guides/getstarted) and working through some of the introductory tutorials.

Background
---
AOP has published a subset of AOP (L3) data products for a handful of NEON sites on GEE. This data has been converted to Cloud Optimized Geotif (COG) format. NEON L3 lidar and derived spectral indices are avaialable in geotif raster format, so are relatively easy to add to GEE, however the hyperspectral data is available in hdf5 (hierarchical data format), and have been converted to the COG format prior to being added to GEE. 

To interactively explore NEON data available on GEE, you can use the [aop-data-visualization](https://neon-aop.users.earthengine.app/view/aop-data-visualization) app created by AOP Scientist John Musinsky. 

Data Availability & Access
---
The NEON data products that have been made available on GEE can be accessed through the `projects/neon` folder with an appended prefix of the Data Product ID, matching the [NEON data portal](https://data.neonscience.org/data-products/explore). The table below summarizes the prefixes to use for each data product.

| Acronym | Data Product      | Data Product ID (Prefix) |
|----------|------------|-------------------------|
| SDR | Surface Directional Reflectance | DP3-30006-001_SDR |
| RGB | Red Green Blue (Camera Imagery) | DP3-30010-001_RGB |
| CHM | Canopy Height Model | DP3-30015-001_CHM |
| DSM | Digital Surface Model | DP3-30024-001_DSM |
| DTM | Digital Terrain Model | DP3-30024-001_DTM |

The table below summarizes the sites, products, and years of NEON AOP data that can currently be accessed in GEE. The * indicates partial availability.

| Domain/Site | Years      | Data Products        |
|----------|------------|-------------------------|
| D08 TALL | 2017, 2018 | SDR, RGB, CHM, DSM, DTM |
| D11 CLBJ | 2017, 2019 | SDR, RGB, CHM, DSM, DTM |
| D14 JORN | 2017, 2019 | SDR, RGB*, DSM, DTM|
| D14 SRER | 2017, 2018, 2019, 2021* | SDR, RGB, CHM*, DSM, DTM|
| D16 WREF | 2017, 2018 | SDR, RGB, CHM, DSM, DTM |
| D17 TEAK | 2017, 2018 | SDR, RGB, CHM, DSM, DTM |

Get Started
---
In this exercise, we will look at hyperspectral data over the Santa Rita Experimental Range collected in 3 years between 2018 and 2021.

Once you have set up your Google Earth Engine account you can navigate to the code editor: https://code.earthengine.google.com/

We will work through a basic exercise consisting of the following steps:

1) Pull in an aop data set
2) Set visualiation parameters
3) Mask no data values
4) Add the AOP layer to the GEE Map
5) Center on the region of interest

You can follow along with this exercise in the code editor. 

1) Read in the SRER 2021 SDR image, using `ee.Image`. We will assign this image to a variable (`var`) called `SRER_SDR2021`.

```javascript
var SRER_SDR2021 = ee.Image("projects/neon/D14_SRER/L3/DP3-30006-001_D14_SRER_SDR_2021");
```

2) Set the visualization parameters. 

To do this we create a new variable (called `visParams`). This variable is applied to the layer and determines how and what is displayed. In this we are setting the RGB bands to display - for this exercise we are setting them to red, green, and blue portions of the spectrum in order to show a True Color Image. You can change these bands to show a False Color Image or any band combination of interest. Please refer to the lesson ___ for more background on band stacking.

```javascript
var visParams = {'min':2,'max':20,'gamma':0.9,'bands':['band053','band035','band019']};
```

3) Mask layers to only show values > 0

This step is optional, but recommended. AOP sets No Data Values to -9999, so if you don't mask out these No Data Values you will see these No Data Values as black in the image. To show only the data that was collected, we recommend masking these values by creating a mask variable, using the `updateMask` function. Reflectance values range between 0 and 1 (CHECK IF THIS DATA IS SCALED OR UNSCALED).

```javascript
var SRER_SDR2021mask = SRER_SDR2021.updateMask(SRER_SDR2021.gte(0.0000));
```

4) Now that we've defined the data, the visualization parameters, and the mask, we can add this Layer to the Map! To do this, we use the `Map.addLayer` function with our masked data variable, `SRER_SDRmask`, suing the `visParams` and assign this layer a label, which will show up in the Map.

```javascript
Map.addLayer(SRER_SDR2021mask, visParams, 'SRER 2021');
```

5) Center the map on our area of interest. GEE by default does not know where we are interested in looking. We can center the map over our new data layer by 

```javascript
Map.setCenter(-110.83549, 31.91068, 11);
```

Putting it All Together
---
The following code chunk runs all the steps we just broke down, and also adds in 2 more years of data. You can copy and paste this into the GEE code editor and run to explore the 3 years of SRER SDR data.

```javascript
// This script pulls in hyperspectral data over the Santa Rita Experimental Range (SRER)
// from 2018, 2019, and 2021 and plots RGB 3-band composites

// Read in Surface Directional Reflectance (SDR) Images 
var SRER_SDR2018 = ee.Image("projects/neon/D14_SRER/L3/DP3-30006-001_D14_SRER_SDR_2018");
var SRER_SDR2019 = ee.Image("projects/neon/D14_SRER/L3/DP3-30006-001_D14_SRER_SDR_2019");
var SRER_SDR2021 = ee.Image("projects/neon/D14_SRER/L3/DP3-30006-001_D14_SRER_SDR_2021");

// Set the visualization parameters so contrast is maximized, and set display to show RGB bands 
var visParams = {'min':2,'max':20,'gamma':0.9,'bands':['band053','band035','band019']};

// Mask layers to only show values > 0 (this hides the no data values of -9999) 
var SRER_SDR2018mask = SRER_SDR2018.updateMask(SRER_SDR2018.gte(0.0000));
var SRER_SDR2019mask = SRER_SDR2019.updateMask(SRER_SDR2019.gte(0.0000));
var SRER_SDR2021mask = SRER_SDR2021.updateMask(SRER_SDR2021.gte(0.0000));

// Add the 3 years of SRER SDR data as layers to the Map:
Map.addLayer(SRER_SDR2018mask, visParams, 'SRER 2018');
Map.addLayer(SRER_SDR2019mask, visParams, 'SRER 2019');
Map.addLayer(SRER_SDR2021mask, visParams, 'SRER 2021');

// Center the map on SRER & zoom to desired level
Map.setCenter(-110.83549, 31.91068, 11);
```
