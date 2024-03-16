# VTUAD: Vessel Type Underwater Acoustic Data

A curated dataset containing underwater acoustic signals categorized into five different classes based on the vessel type: Cargo, Tanker, Tug, Passengership, and Background. Different subsets of data were generated from the original data considering the distance from the vessel to the hydrophone picking up the vessel's sound. These subsets, or scenarios, were created considering inclusion and exclusion radii: the first scenario has an inclusion radius of 2km and an exclusion radius of 3km; the second is defined within the interval of 3km and 4km between inclusion and exclusion radius; the third scenario is defined within the interval between 4km and 6km. Also, environmental information, obtained from the CTD recorder, is available, containing five different signals: temperature, measured in Celsius; conductivity, measured in siemens per metre; pressure, in decibar; salinity, measured in psu; and sound speed, measured in meters per second. For every different instance, the average of the measures was considered.

## Origin of the data
The data used in this dataset consists of signals obtained from the [Ocean Network Canada initiative](https://www.oceannetworks.ca/), captured during the deployment at the Strait of Georgia, Canada, from June 24 to November 3, 2017, representing typical pre-pandemic operation during the summer and autumn seasons. An icListen AF Hydrophone, located 147 meters below sea level, was used to obtain the acoustic signals. In addition, the positional information about the vessels was obtained using the Automatic Identification System (AIS) data. The CTD recorder used to capture the environmental signals was the Sea-Bird SBE 16plus SEACAT Recorder, a high accuracy recorder designed to fixed-side deployments.

## Folder structure

The files are structured as follows:

* **inclusion_xxxx_exclusion_yyyy:** The folder containing the data related to the scenario defined by the redius between *xxxx* and *yyyy*.
  * **background**: All the `.wav` files related to the background noise (absence of vessels in the scenario).
  * **vessel**: The `.wav` files related to the four categories of vessels: Cargo, Tanker, Tug, and Passengership.
  * **metadata.cvs**: The file containing the information of each audio category and the environmental variables.

## Metadata Description

The description of each field of the metadata is as follows:

* **label**: The category of the vessel (Cargo, Tanker, Tug, Passengership, or Background). If the vessel is not from any of the categories, it is attributed the category `other`;
* **duration_sec**: The duration of the audio in seconds;
* **path**: The relative path (considering the root folder of the scenario) to the `.wav` file;
* **sample_rate**: The sample rate of the recording;
* **class_code**: The class code of the vessel in the audio. See [NOAA’s Office for Coastal Management](https://coast.noaa.gov/data/marinecadastre/ais/VesselTypeCodes2018.pdf) as reference;
* **date**: The date of acquisition in the format YYYYMMDD;
* **MMSI**: The MMSI code of the vessel;
* **t1**: The temperature, measured in Celsius;
* **c1**: The conductivity, measured in siemens per metre;
* **p1**: The pressure, measured in decibar;
* **sal**: The salinity, measured in psu;
* **sv**: The sound speed, measured in meters per second;
* **t1_norm**,**c1_norm**,**p1_norm**,**sal_norm**,**sv_norm**: The same variables, but normalized (range from 0 to 1) using the maximum and minumum values as reference.