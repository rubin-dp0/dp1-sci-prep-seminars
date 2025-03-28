# Extracting a light curve of a variable object 

For the Portal Aspect of the Rubin Science Platform at data.lsst.cloud.

**Data Release:** DP0

**Last verified to run:** 2025-03-30

**Learning objective:** Use the ADQL interface to extract multi-band photometry of a varible object

**LSST data products:** `Object` catalog, `deepCoadd` image

**Credit:** Based on tutorials developed by the Rubin Community Science team. Please consider acknowledging them if this tutorial is used for the preparation of journal articles, software releases, or other tutorials.

**Get Support:** Everyone is encouraged to ask questions or raise issues in the [Support Category](https://community.lsst.org/c/support/6) of the Rubin Community Forum. Rubin staff will respond to all questions posted there.

## Introduction

AGN are...

The foreground galaxy perceptibly distorts the light from the background galaxy, and the background galaxy appears magnified and multiply-imaged, or as an arc or ring, instead of its true shape.

In this case, the background galaxy is said to be "strongly lensed".

<img src="images/lensing_mock_pink_blue.png" alt="Lensing infographic." width="400"/>

Figure 1: A graphic demonstrating how a distant galaxy (red) appears distorted (pink) due to the mass of a foreground galaxy (blue) from Earth's perspective (right side), and appears as a strongly-lensed object in an image (left side).


**Data Preview 0.2 vs. Data Preview 1**

In the Data Preview 0.2 (DP0.2) simulation there are no strongly-lensed objects, like there will be in the real data released as Data Preview 1 (DP1).
None of the "potential foreground lens" galaxies explored in this tutorial will have any lensed objects nearby.
Furthermore, for DP1 the exact types of measurements and their column names are likely to be different, compared to DP0.
The LSST Science Pipelines have evolved considerably since being run on the DP0.2 simulation. 

**This is not an introductory-level tutorial!**
Find tutorials on the Portal's User Interface, ADQL interface, and the Results Viewer in the [DP0.2 documentation](dp0-2.lsst.io).

**Related tutorials relevant to strong lensing science.**
See also the DP0.2 portal tutorials on exploring extended object populations, and the SAOImage DS9-like functionalities of Firefly.

## 1. Execute the ADQL query.

### 1.1. Log in to the RSP Portal.

In a browser, go to the URL [data.lsst.cloud](https://data.lsst.cloud).

Select the Portal Aspect and follow the process to log in.

### 1.2. Navigate to the DP0.2 ADQL interface.

From the top menu bar, select the "DP0.2 Catalogs" tab.

Notice that various tables are available in the drop-down menus.

Notice also that query constraints can be set up in this table interface.

At upper right, click the toggle to "Edit ADQL".

### 1.3. Execute the ADQL query.

Copy and paste the following into the ADQL Query box.

At lower left, click the blue "Search" button.

~~~~mysql    
SELECT objectId, coord_dec, coord_ra, g_cModelFlux, r_cModelFlux, i_cModelFlux 
  FROM dp02_dc2_catalogs.Object 
  WHERE CONTAINS(POINT('ICRS', coord_ra, coord_dec),CIRCLE('ICRS', 62.3, -38.4, 1))=1
    AND (detect_isPrimary =1 AND refExtendedness =1
      AND r_cModelFlux <575000 AND r_cModelFlux >91000
      AND g_cModelFlux >36000 AND i_cModelFlux <575000
      AND g_cModelFlux < r_cModelFlux AND r_cModelFlux < i_cModelFlux)
~~~~

**About the query.**

The query selects 6 columns to be returned from the DP0.2 `Object` table.

* an object identifier (integer)
* the coordinates right ascension and declination
* object flux measurements in the g, r, and i filters

The query constrains the results to only include rows (objects) that are:

* in the search area (within a 1 degree radius of RA, Dec = 62.3, -38.4 deg)
* not a duplicate or parent object (`detect_isPrimary` = 1)
* an extended object, not a point-like source (`refExtendedness` = 1)
* bright in r-band ($17 < r < 19$ mag)
* not faint in g-band ($g < 20$ mag)
* not near LSST saturation in i-band ($17 > i$ mag)
* red; is brighter in successively redder filters ($i < r < g$ mag

Details about the object flux measurements:

* Photometric measurements are stored as fluxes in the tables, not magnitudes.
* `Object` table fluxes are in nJy, and the conversion is: $m = -2.5\log(f) + 31.4$.
* The SDSS [Composite Model Magnitudes](https://www.sdss3.org/dr8/algorithms/magnitudes.php#cmodel)
or `cModel` fluxes are used.

## 2. Choose an extended object.

### 2.1. Confirm the results view.

The query should have returned 1004 objects.

The results view should appear similar to the figure below (panel size ratios or colors may differ).

<img src="images/screenshot_1.png" alt="Default results view." width="400"/>

Figure 2: The default results view after running the query. At upper left, the [HiPS](https://aladin.cds.unistra.fr/hips/) coverage map with returned objects marked individually, or in [HEALPix](https://sourceforge.net/projects/healpix/) regions (diamonds). At upper right, the active chart plots 2 columns by default. Below is the table of returned data.

### 2.2. Select an object.

Large scale clustering of the bright red extended objects can be seen in the active chart.

Click on any point in one of the clumps, and it will be highlighted in all three panels.

In the coverage map at upper left, zoom in on the selected point in the HiPS map.

<img src="images/screenshot_2.png" alt="Zoom in one one interesting galaxy." width="400"/>

Figure 3: The results view after selecting an object and zooming in on the coverage chart.

## 3. View the object in the deep coadd.

The HiPS maps are intended for quicklook and data discovery, not scientific analysis, but the corresponding `deepCoadd` images can be retrieved.

### 3.1. Select the object in the table.

Click the box in the leftmost column of the table to select the row.

### 3.2. Create an image query for the selected object.

In the table's upper right corner, there are several icons.

Hover over the first in the row, and the pop-up "Search drop down: search based on table" will appear.

Click the icon to see the search drop down menu.

Click on "Search ObsTAP for images at row".

<img src="images/screenshot_3.png" alt="Search drop down." width="400"/>

Figure 4: The search drop down menu.


### 3.3. Search ObsTAP for images

The default query is a search for any kind of image.

Update the query to only search for deep coadd images.

At left, under "Calibration Level", click the box next to 3, and under "Data Product Subtype" select `lsst.deepCoadd_calexp`.

Click the blue "Search" button at lower left.

<img src="images/screenshot_4.png" alt="Search drop down." width="400"/>

Figure 5: The ObsTAP interface set to search for deep coadd images of the selected object.


### 3.4. View the object in the deep coadd image

Twelve deep coadd images, two per LSST filters u, g, r, i, z, and y, are retrieved because deeply coadded images overlap at the edges, and the object was in the overlap zone.

The image that is selected in the table will display in the upper-left panel (the HiPS map is still there in the Coverage tab).

Objects from the first query will be marked on the image.

Zoom in on the object of interest.

<img src="images/screenshot_5.png" alt="Search drop down." width="400"/>

Figure 6: The r-band deep coadd image, zoomed in on the object of interest.


## 4. Exercises for the learner.

Feel free to simply play around in the Portal.

The image viewer interface is called "Firefly".

It has a toolbar with functionality such as image scaling, recentering, line cut plots, and so on.

The cutout functionality is still in development.

Click on icons and try the tools.

The button to restore defaults is under the wrench-and-hammer icon.



