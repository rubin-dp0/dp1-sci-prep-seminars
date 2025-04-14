# Stars and Milky Way SED Demo

For the Portal Aspect of the Rubin Science Platform at data.lsst.cloud.

**Data Release:** DP0

**Last verified to run:** 2025-04-11

**Learning objective:** Use the ADQL interface to query for stars, generate an SED plot for 5 stars.

**LSST data products:** `Object` catalog

**Credit:** Based on tutorials developed by the Rubin Community Science team. Please consider acknowledging them if this tutorial is used for the preparation of journal articles, software releases, or other tutorials.

**Get Support:** Everyone is encouraged to ask questions or raise issues in the [Support Category](https://community.lsst.org/c/support/6) of the Rubin Community Forum. 
Rubin staff will respond to all questions posted there.

## Introduction

This tutorial uses an ADQL query to search for bright stars in a small region of sky, and then uses the Results interface to create a photometric SED. 
 

**Data Preview 0.2 vs. Data Preview 1**

In the Data Preview 0.2 (DP0.2) simulation stars are quantized unlike the real data released as Data Preview 1 (DP1).
Studying photometric SED within the DP0.2 dataset is still useful as the techniques used with the simulated data are comparable real data techniques.
However, for DP1 the exact types of measurements and their column names are likely to be different, compared to DP0.
The LSST Science Pipelines have evolved considerably since being run on the DP0.2 simulation. 

**Related tutorials relevant to stellar science.**
See also the DP0.2 portal tutorials on plotting histograms, light curves, extracting pixel values, and the SAOImage DS9-like functionalities of Firefly.

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

In the row limit box, type '5', then click the blue "Search" button.

~~~~mysql 
SELECT objectId, coord_ra, coord_dec, detect_isPrimary,
       u_calibFlux, 376 AS u_wave,
       g_calibFlux, 427 AS g_wave,
       r_calibFlux, 628 AS r_wave,
       i_calibFlux, 709 AS i_wave,
       z_calibFlux, 833 AS z_wave,
       y_calibFlux, 978 AS y_wave
FROM dp02_dc2_catalogs.Object
WHERE CONTAINS(POINT('ICRS', coord_ra, coord_dec), CIRCLE('ICRS', 62, -37, 1)) = 1
      AND detect_isPrimary = 1
      AND u_calibFlux > 360
      AND u_extendedness = 0
      AND g_calibFlux > 360
      AND g_extendedness = 0
      AND r_calibFlux > 360
      AND r_extendedness = 0
      AND i_calibFlux > 360
      AND i_extendedness = 0
      AND z_calibFlux > 360
      AND z_extendedness = 0
      AND y_calibFlux > 360
      AND y_extendedness = 0
~~~~
**About the query.**

The query creates 18 columns to be returned from the DP0.2 `Object` table.

* an object identifier (integer)
* the coordinates right ascension and declination
* object flux measurements in the u, g, r, i, z, and y filters
* adds columns for filter wavelengths, this will simplify plotting the SED

The query constrains the results to only include rows (objects) that are:

* in the search area (within a 1 degree radius of RA, Dec = 62, -37 deg)
* not a duplicate or parent object (`detect_isPrimary` = 1)
* an not extended object, a point-like source (`refExtendedness` = 0)
* bright in all bands (band_calibFlux > 360)

Details about the object flux measurements:

* Photometric measurements are stored as fluxes in the tables, not magnitudes.
* `Object` table fluxes are in nJy, and the conversion is: $m = -2.5\log(f) + 31.4$.

## 2. Choose an extended object.

### 2.1. Confirm the results view.

The query should have returned 1004 objects.

The results view should appear similar to the figure below (panel size ratios or colors may differ).

<img src="images/SED_returned_ADQL_query.PNG" alt="Results of ADQL query." width="400"/>

Figure 2: The results view after running the query. (Note: in this image the results presented are the data table for 5 stars and a plot of ra/dec of each of those
stars. This query was run directly after the CMD tutorial and the results view is still in the Table/Coverag-Image-Charts selection.)


## 3. Plot the SED.

Plotting a SED in the portal using the ADQL query above is easy, but still requires a few steps.

### 3.1. Click on the gear icon.

Click the gear icon in the right hand panel to alter the plot. Change the parameters as shown in Figure 3, 

<img src="images/SED_returned_ADQL_query.PNG" alt="Results of ADQL query." width="400"/>

Figure 3: Modify plot - parameters for u-band.

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


