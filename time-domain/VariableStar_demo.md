# Searching for variable stars

For the Portal Aspect of the Rubin Science Platform at data.lsst.cloud.

**Data Release:** DP0

**Last verified to run:** 2025-04-07

**Learning objective:** Use the ADQL interface to query for bright red extended objects that might be potential foreground lenses. Investigate the r-band `deepCoadd` image of a galaxy.

**LSST data products:** `Object` catalog, `deepCoadd` image

**Credit:** Based on tutorials developed by the Rubin Community Science team. Please consider acknowledging them if this tutorial is used for the preparation of journal articles, software releases, or other tutorials.

**Get Support:** Everyone is encouraged to ask questions or raise issues in the [Support Category](https://community.lsst.org/c/support/6) of the Rubin Community Forum. Rubin staff will respond to all questions posted there.

## Introduction



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

At the lower left, click the blue "Search" button.

~~~~mysql    
SELECT ra, decl, diaObjectId, nDiaSources, gPSFluxNdata,
    gPSFluxSigma, gPSFluxMean,
    gPSFluxStetsonJ, gPSFluxChi2, gPSFluxMAD,
    gPSFluxPercentile25, gPSFluxPercentile75, gTOTFluxMean,
    rPSFluxStetsonJ, rPSFluxChi2, rPSFluxMAD, 
    rPSFluxMean, rPSFluxSigma, rPSFluxLinearSlope,
    scisql_nanojanskyToAbMag(gTOTFluxMean) as gmag,
    scisql_nanojanskyToAbMag(rTOTFluxMean) as rmag
    FROM dp02_dc2_catalogs.DiaObject
    WHERE nDiaSources > 10
        AND rTOTFluxMean < 1e5
        AND rPSFluxMax < 1e5
        AND rPSFluxMin > -1e5
        AND rPSFluxNdata > 8
        AND CONTAINS(POINT('ICRS', ra, decl), CIRCLE('ICRS', 56.1, -33.2, 5)) = 1
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

The query should have returned 36322 objects.

The results view should appear similar to the figure below (panel size ratios or colors may differ).

<img src="images/screenshot_1.png" alt="Default results view." width="400"/>

Figure 2: The default results view after running the query. At upper left, the [HiPS](https://aladin.cds.unistra.fr/hips/) coverage map with returned objects marked individually, or in [HEALPix](https://sourceforge.net/projects/healpix/) regions (diamonds). At upper right, the active chart plots 2 columns by default. Below is the table of returned data.

### 2.2. XXXXXXXX

Hide the coverage map by clicking the "hamburger" menu (3 horizontal lines) at the upper left, then selecting the following "results layout" from the menu:

<img src="images/screenshot_2.png" alt="Selected results layout." width="400"/>

Figure 3: ZZZZZZZZ

Highlight the "active chart" tab in the right half of the screen. Click the "+" sign at the upper left of the tab to bring up the "Add New Chart" dialog.

Use the default plot type of "scatter", and select `rPSFluxMean` for the X axis, and `rPSFluxLinearSlope` for the Y axis. Then click "OK" to create the plot. (You can close the original RA, Dec heatmap figure at this point by clicking the "x" at its top right corner.)

Create another new scatter plot, this time with "rPSFluxSigma" vs "rPSFluxMean". You should now have two scatter plots side-by-side.

Select the plume of objects with large negative `rPSFluxLinearSlope` values and slightly positive `rPSFluxMean`.

Use the "box select" tool:

<img src="images/screenshot_3.png" alt="Box select tool." width="400"/>

Select a box, then click the double check-mark at the top to select the points. Then you should see something like this:

<img src="images/screenshot_4.png" alt="Selected results layout." width="400"/>

Select one of the highlighted objects, then change to the "Data Products: dp02_dc2_catalogs.DiaObject - data" tab.

Under the "More" dropdown menu, select "Show: Retrieve DiaSource time series", then click the blue "Submit" button at lower left. By default, a table will appear. Change to the "Chart" by clicking the toggle at the top of the tab.

By default, this should show the first two columns of the table, which for a time series will be "apFlux (nJy)" vs. "expMidptMJD (d)". It should look something like the following figure (though the data will differ depending which point you selected):

<img src="images/screenshot_5.png" alt="Time series plot of a selected object." width="400"/>

### 2.2. Select candidate variable stars

Change the two scatter plots to instead show `gPSFluxStetsonJ` vs. `gmag`, and `gPSFluxChi2` vs. `gmag`.

Next, in the table view, select only points with more than 30 g-band measurement by adding the constraint ">30" in the `gPSFluxNdata` column.

<img src="images/screenshot_6.png" alt="Applying constraints on number of g-band detections." width="400"/>

This should reduce the number of selected objects to 499, and your plots should look like the following:

<img src="images/screenshot_7.png" alt="Variable star plots." width="400"/>

Select a relatively bright outlier (e.g., with `gmag` < 21) that lies above the blob of points clustered at `gPSFluxStetsonJ` = 0 and `gPSFluxChi2` = 0.

As before, switch to the "Data product: " tab. Under the "More" dropdown, select "Show: Retrieve ForcedSourceOnDiaObject time series" (note that we want the ForcedSourceOnDiaObject here, because near its mean flux, a variable object will not produce a DiaSource).

For clarity, select a single band (e.g., "g" or "r") before plotting the time series. Then click "Submit" at the bottom, and you should obtain a plot that looks similar to the following:

<img src="images/screenshot_8.png" alt="Time series of a variable star candidate." width="400"/>

This object's flux is clearly fluctuating by a fair amount. It seems like a good candidate to be a variable star! Now you may want to extract all the time series data for this particular object and do more detailed periodicity analysis offline, _or_ note its DiaObjectID and use that to extract its light curve in the Notebook aspect of the RSP.


