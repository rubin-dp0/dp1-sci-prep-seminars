# Studying active galactic nuclei:  extracting a light curve of a variable object and extracting an image of a host galaxy

For the Portal Aspect of the Rubin Science Platform at data.lsst.cloud.

**Data Release:** DP0

**Last verified to run:** 2025-03-30

**Learning objective:** Use the ADQL interface to extract multi-band photometry of a variable object

**LSST data products:** `Object` catalog, `deepCoadd` image

**Credit:** Based on tutorials developed by the Rubin Community Science team. Please consider acknowledging them if this tutorial is used for the preparation of journal articles, software releases, or other tutorials.

**Get Support:** Everyone is encouraged to ask questions or raise issues in the [Support Category](https://community.lsst.org/c/support/6) of the Rubin Community Forum.
Rubin staff will respond to all questions posted there.

## Introduction

AGN are luminous point-like sources present in centers of many galaxies, and often are much brighter than the host galaxy.
Those are characterized by a distinct spectrum - usually are bluer than the stars in the host galaxy, and exhibit prominent emission lines.
Their broad-band spectra often extend from the radio regime to X-ray and even gamma-ray bands.
AGN often vary in all observable spectral bands on a wide range of time scales.
This implies a very compact source, typically smaller than a light-year.
The current widely accepted model of an AGN is that the source of power is the accretion of galaxian matter onto the central black hole, releasing its gravitational energy into heat.

<img src="images/AGN_Unified.png" alt="AGN Infographic." width="400"/>

Figure 1: A graphic demonstrating various types of active galaxies.
It illustrates that the appearance of a sub-class might depend on the orientation of the axis of symmetry (defined by the accretion disk) with respect to our line of sight.


**Data Preview 0.2 vs. Data Preview 1**

In the Data Preview 0.2 (DP0.2) simulation there are no AGN, but very likely there will be AGN in the real data released as Data Preview 1 (DP1).
However, the DP0.2 contains variable stars and we will pretend one such star is an AGN.
Furthermore, for DP1 the exact types of measurements and their column names are likely to be different, compared to DP0.
The LSST Science Pipelines have evolved considerably since being run on the DP0.2 simulation. 

Here, you will learn how to plot the light curve of a difference image analysis object (diaObject).
In this demonstration, an RR Lyrae star is used. This star has coordinates RA, Dec = 62.1479031, -35.7991348 deg.
This object has an identifier number ``diaObjectId`` = 1651589610221862935.
The ``diaObjectId`` for a given coordinate can be obtained from the ``DiaObject`` table by
making a spatial query on the coordinates with a small radius (a few arcseconds) that returns the ``diaObjectId`` column.
As is appropriate for steady variable objects (rather than transients such as a supernova), the forced photometry fluxes from PSF model fits in the direct (not difference) images are used.

**This is an introductory-level tutorial, aimed at users who want to get started conducting AGN science**
Find tutorials on the Portal's User Interface, ADQL interface, and the Results Viewer in the [DP0.2 documentation](dp0-2.lsst.io).

**Related tutorials relevant to AGN science.**
See also the DP0.2 portal tutorials on exploring transient and variable sources, and specifically the one presented last week at the Rubin Assembly covering that subject.  

## 1. Execute the ADQL query.

### 1.1. Log in to the RSP Portal.

In a browser, go to the URL [data.lsst.cloud](https://data.lsst.cloud).

Select the Portal Aspect and follow the process to log in.

### 1.2. Determine the ``DiaObjectId`` for the object of interest.

Navigate to the DP0.2 UI interface by selecting the "UI assisted" view on the uppoer right.

From the top menu bar, select the "DP0.2 Catalogs" in the "Table Collection (Schema)" tab, and "dp02_dc2_catalogs_DiaObject" in the "Tables" tab.

Select ``diaObjectId`` box and the ``nDiaSources`` in the table on the right.
Under "Enter Constraints" uncheck "temporal" and check "spatial" box.
Enter the coordinates of the object of interest - 62.1479031, -35.7991348 and 3 arc sec as the radius.
Click "Search" - the resulting table on the bottom will show that the ``diaObjectId`` with the largest number of sources (366) is indeed 1651589610221862935.

<img src="images/diaObjectId.png" alt="diaObjectId." width="400"/>

Figure 2: the Portal UI interface ready to retrieve the ``diaObjectId``.


### 1.3. Navigate to the DP0.2 ADQL interface.

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



