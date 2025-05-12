# Time Domain

## Hosts

Ryan Lau, Jeff Carlin

## Slides

[slide deck](https://docs.google.com/presentation/d/1lmPyC9gaYqPzZlU9GUb0-5Gw8j54vGRwukANL1D5Dj4/edit?usp=sharing) (read only)
[slide deck for 12 May 2025 Science Seminar](https://docs.google.com/presentation/d/1pPvUeQ4ljMOSrG7NeZWC9Gu_tGfyhTRzzllkx3EZWzs/edit?usp=sharing) (read only)

## Tutorial

We will go through a portal demonstration and two notebooks (time permitting):

`Transient_Variable_demo.md`

* Query the DiaObject table, and plot various statistical quantities to identify transient and variable objects.
* Identify a supernova candidate and plot its time series from DiaSource.
* Identify a variable star candidate and plot its time series from ForcedSourceOnDiaObject.

[DiaObject_Anomaly_Detection.ipynb](https://github.com/lsst/tutorial-notebooks/blob/main/DP0.2/17_DiaObject_Anomaly_Detection.ipynb) (DP0.2 tutorial notebook 17)

* Use an Isolation Forest algorithm on the DP0.2 DiaObject Table to identify anomalies.
* Plot the results and inspect anomalies and lightcurves of anomalous objects.
* Display calexp, difference template, and difference images using Butler.

`VariableStar_Sci_Demo_using_AnomalyDetection.ipynb`

* Use an Isolation Forest algorithm on the DP0.2 DiaObject Table to identify candidate variable stars.
* Plot the results and inspect anomalies.
* Run a Lomb-Scargle algorithm to determine periods, then create phased lightcurves of candidate variable stars.
