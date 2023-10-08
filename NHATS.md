NHATS API
=========

**Version: 1.3** (2019 September)  
[change log](#change_log)

The NHATS API provides a method of requesting data from the NHATS-related tables in the SBDB. These data will primarily support the CNEOS “Accessible NEAs” web-page. Please see the [NHATS web-page](https://cneos.jpl.nasa.gov/nhats/) for details about NHATS.

HTTP Request
------------

GET `https://ssd-api.jpl.nasa.gov/nhats.api`

### Example Queries

* get summary data for available NHATS objects using various constraints:
    * `https://ssd-api.jpl.nasa.gov/nhats.api` (unconstrained)
    * `https://ssd-api.jpl.nasa.gov/nhats.api?dv=6&dur=360&stay=8&launch=2020-2045&h=26&occ=7` (web-page “standard” constraints)
* get details for the specified NHATS object:
    * `https://ssd-api.jpl.nasa.gov/nhats.api?des=99942` (99942 Apophis, default constraints )
    * `https://ssd-api.jpl.nasa.gov/nhats.api?des=2000%20SG344&dv=6&dur=360&stay=8&launch=2020-2045` (2000 SG344, constrained)

Query Parameters
----------------

This API supports two query modes.

* **S** \- obtain summary data spanning all available NHATS object
* **O** \- obtain details related to a specified NHATS object

| Parameter | Type | Default | Allowable Values | Function | Modes | Description |
| --- | --- | --- | --- | --- | --- | --- |
| dv  | int | `12` | `4`,`5`,`6`,`7`,`8`,  <br>`9`,`10`,`11`,`12` | filter | S,O | Minimum total delta-V (km/s) |
| dur | int | `450` | `60`,`90`,`120`,`150`,`180`,  <br>`210`,`240`,`270`,`300`,`330`,  <br>`360`,`390`,`420`,`450` | filter | S,O | Minimum total duration (days) |
| stay | int | `8` | `8`,`16`,`24`,`32` | filter | S,O | Minimum stay (days) |
| launch | string | `2020-2045` | `2020-2025`,`2025-2030`,  <br>`2030-2035`,`2035-2040`,  <br>`2040-2045`,`2020-2045` | filter | S,O | Launch window (year range) |
| h   | int | none | `16`,`17`,`18`,`19`,`20`,  <br>`21`,`22`,`23`,`24`,`25`,  <br>`26`,`27`,`28`,`29`,`30` | filter | S   | Object’s maximum absolute magnitude, H (mag) |
| occ | int | none | `0`,`1`,`2`,`3`,`4`,  <br>`5`,`6`,`7`,`8` | filter | S   | Object’s maximum orbit codition code (OCC) |
| spk | int | none |     | selector | O   | select data for the object matching this SPK-ID (e.g., `2000433` ) |
| des | string | none |     | selector | O   | select data for the object matching this designation (e.g., `2015 AB` or `141P` or `433`) |
| plot | boolean | false | `0`,`1`,`false`,`true` | option | O   | include base-64 encoded plot image file content via output field `plot_base64` |

The presence of an object selector query parameter (`des` or `spk`) distinguishes between the two modes (**S** and **O**). That is, if an object selector parameter is specified, an object detail query is executed (mode **O**). If no object selector parameter is specified, a summary query is executed (mode **S**).

Note that submitting a query parameter `h` along with `des` or `spk` results in an error because mode **O** (implied by `des` or `spk`) does not match mode **S** (implied by the presence of `h`).

Data Output
-----------

> Please **always check** the JSON payload “signature” object for the API “version”. If the version does not match the version in this document (at the top), there is no guarantee that the format has not changed.
> 
> Example "signature" object with "version" value "1.0": `"signature":{"version":"1.0","source":"NASA/JPL ... API"}`

Data are returned in JSON format. The specific structure used is dependent on the query mode.

### Mode S

For summary queries (mode **S**), there may be no records matching the specified constraints. In such cases, the data count will be zero and the data array will be empty (or null). Normal results will contain a count of the number or records and an array of records with the following content.

* fullname
* constraints used (is this necessary? - convenient when defaults are selected)
* NHATS object details
* NHATS limits minima

The following is an example JSON-format data result matching three objects:

    {
      "signature":{"version":"1.0","source":"NASA/JPL NHATS Data API"},
      "count":3,
      "data":[
        {
          "des":"1943",
          "fullname":"1943 Anteros (1973 EC)",
          "orbit_id":"360",
          "h":"15.8",
          "min_size":"1231",
          "max_size":"5507",
          "size":"2300",
          "size_sigma":null,
          "occ":"0",
          "min_dv":{"dv":"11.980", "dur":"354"},
          "min_dur":{"dv":"11.980","dur":"354"},
          "n_via_traj":14,
          "obs_start":"2016-05-11",
          "obs_end":"2017-03-04",
          "obs_mag":"16.96",
          "obs_flag":" ",
          "radar_obs_a":"2026-06-12",
          "radar_snr_a":"860",
          "radar_obs_g":"2026-05-30",
          "radar_snr_g":"34"
        },
        {
          "des":"2000 SG344",
          "fullname":"(2000 SG344)",
          "orbit_id":"15",
          "h":"24.7",
          "min_size":"20",
          "max_size":"89",
          "size":null,
          "size_sigma":null,
          "occ":"2",
          "min_dv":{"dv":"3.550", "dur":"370"},
          "min_dur":{"dv":"3.996","dur":"306"},
          "n_via_traj":3302718,
          "obs_start":"2028-04-28",
          "obs_end":"2028-09-11",
          "obs_mag":"19.11",
          "obs_flag":" ",
          "radar_obs_a":"2028-05-07",
          "radar_snr_a":"3.e3",
          "radar_obs_g":"2028-05-07",
          "radar_snr_g":"59"
        },
        {
          "des": "2006 RH120",
          "fullname":"(2006 RH120)",
          "orbit_id":"48",
          "h":"29.5",
          "min_size":"2.2",
          "max_size":"10",
          "size":null,
          "size_sigma":null,
          "occ":"1",
          "min_dv":{"dv":"3.972", "dur":"450"},
          "min_dur":{"dv":"3.996","dur":"434"},
          "n_via_traj":1277927,
          "obs_start":"2028-06-27",
          "obs_end":"2028-07-12",
          "obs_mag":"23.2",
          "obs_flag":" ",
          "radar_obs_a":null,
          "radar_snr_a":null,
          "radar_obs_g":null,
          "radar_snr_g":null
        }
      ]
    }
    

### Mode O

For object detail queries (mode **O**), it is possible to get no matching results for a valid NHATS object if the constraints are too limiting. In such cases, the JSON payload will contain an error keyword with appropriate message.

    { "error" : "no matching results for specified object and constraints" }
    

Similarly, in cases where the specified object is not an NHATS object, the JSON payload will contain a message similar to the following.

    { "error" : "specified NHATS object not found"" }
    

Normal results will contain the following JSON structure.

* fullname
* NNATS object details
* constraints used
* NHATS trajectory details (two trajectory records: min\_dv and min\_dur)
* plot file image contents base64-encoded (if requested)

The JSON-format result will look something like this:

    {
      "signature":{"version":"1.0","source":"NASA/JPL NHATS Data API"},
      "fullname":"(2000 SG344)",
      "orbit_id":"15",
      "h":"24.7",
      "min_size":"20",
      "max_size":"89",
      "size":null,
      "size_sigma":null,
      "occ":"2",
      "min_dv":{"dv":"3.550", "dur":"370"},
      "min_dur":{"dv":"3.996","dur":"306"},
      "n_via_traj":3302718,
      "obs_start":"2028-04-28",
      "obs_end":"2028-09-11",
      "obs_mag":"19.11",
      "obs_flag":" ",
      "radar_obs_a":"2028-05-07",
      "radar_snr_a":"3.e3",
      "radar_obs_g":"2028-05-07",
      "radar_snr_g":"59",
      "plot_base64":"iVBORw0KGgoAAAAN ... AASUVORK5CYII=",
      "computed":"2012-01-06",
      "min_dv_traj":{
        "tid":"889907",
        "dv_total":"3.601",
        "dur_total":"346",
        "dur_out:"137",
        "dur_at":"32",
        "dur_ret":"177",
        "launch":"2028-04-22",
        "c3":"1.737",
        "v_dep_earth":"1.318",
        "dv_dep_park":"3.256",
        "vrel_arr_neo":"0.128",
        "vrel_dep_neo":"0.217",
        "vrel_arr_earth":"0.000",
        "v_arr_earth":"11.141",
        "dec_dep":"-8.723",
        "dec_arr":"-4.771"
      },
      "min_dur_traj":{
        "tid":"913886",
        "dv_total":"3.996",
        "dur_total":"306",
        "dur_out:"137",
        "dur_at":"48",
        "dur_ret":"121",
        "launch":"2028-04-30",
        "c3":"2.197",
        "v_dep_earth":"1.482",
        "dv_dep_park":"3.277",
        "vrel_arr_neo":"0.159",
        "vrel_dep_neo":"0.559",
        "vrel_arr_earth":"0.000",
        "v_arr_earth":"11.231",
        "dec_dep":"-11.175",
        "dec_arr":"4.603"
      }
    }
    

HTTP Response Codes
-------------------

All errors are returned via appropriate HTTP response codes. Note that it is possible to submit query parameters resulting in no matching data. In such cases, a non-error code of 200 is returned so the user is responsible for checking the payload if they wish to detect a null-result.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal successful result: data returned (may be empty) or an error message (for example, if the des/spk was not found) |
| 400 | Bad Request | the request contained invalid keywords and/or content (details returned in the JSON payload) |
| 405 | Method Not Allowed | the request used an incorrect method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | the database is not available at the time of the request |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

Background Information
----------------------

The primary NHATS data table as rendered in the current NEO web-site contains a number of parameters. These various parameters require database queries of several different tables in the SBDB. In addition, the data is rendered initially with filter parameters applied.

The main NHATS data table currently allows for filtering by placing user-specified constraints on the following parameters:

* total delta-V (e.g., `<= 6 km/s`)
* total duration (e.g., `<= 360 days`)
* stay (e.g., `>= 8 days`)
* launch window (years: e.g., `2020-2045`)
* H magnitude (e.g., `<= 26`)
* OCC (e.g., `<= 7`)

### Primary NHATS Web-Page Data Table Columns

| table.item | web-page table label |
| --- | --- |
| nhats_object.id  <br>object.pdes  <br>object.name  <br>designation.desig | `Object Designation` |
| nhats\_object.orbit\_id | `Orbit ID` |
| nhats_object.H | `H (mag)` |
| nhats\_object.min\_size  <br>nhats\_object.max\_size | `Estimated Diameter (m)` |
| nhats_object.occ | `OCC` |
| nhats\_limits.dv\_min_tdv  <br>nhats\_limits.dur\_min_tdv | `Min. delta-V [delta-V, dur.] (km/s), (d)` |
| nhats\_limits.dv\_min_tdur  <br>nhats\_limits.dur\_min_tdur | `Min. Duration [delta-V, dur.] (km/s), (d)` |
| nhats\_object.n\_via_traj | `Viable Trajectories` |
| nhats\_object.obs\_start  <br>nhats\_object.obs\_mag | `Next Optical Opportunity (yyyy-mm [Vp])` |
| nhats\_object.obs\_flag | `" " = normal optical opportunity; "S" = survey recovery possible` |
| nhats\_object.radar\_obs_a  <br>nhats\_object.radar\_snr_a | `Next Arecibo Radar Opportunity (yyyy-mm [SNR])` |
| nhats\_object.radar\_obs_g  <br>nhats\_object.radar\_snr_g | `Next Goldstone Radar Opportunity (yyyy-mm [SNR])` |

#### Detail (object-specific) NHATS Web-Page Data Table Items

| table.item | web-page table label |
| --- | --- |
| nhats\_traj.dv\_total | `Total Mission delta-V (km/s)` |
| nhats\_traj.dur\_total | `Total Mission Duration (d)` |
| nhats\_traj.dur\_out | `Outbound Flight Time (d)` |
| nhats\_traj.dur\_at | `Stay Time (d)` |
| nhats\_traj.dur\_ret | `Inbound Flight Time (d)` |
| nhats_traj.launch | `Launch date (YYYY-MM-DD)` |
| nhats_traj.c3 | `C3 (km2/s2)` |
| nhats\_traj.v\_dep_earth | `Departure Vinfinity (km/s)` |
| nhats\_traj.dv\_dep_park | `Earth Departure dV (km/s)` |
| nhats\_traj.vrel\_arr_neo | `dV to Arrive at NEA (km/s)` |
| nhats\_traj.vrel\_dep_neo | `dV to Depart NEA (km/s)` |
| nhats\_traj.vrel\_arr_earth | `Earth return dV (km/s)` |
| nhats\_traj.v\_arr_earth | `Entry Speed (km/s)` |
| nhats\_traj.dec\_dep | `Depature Declination (deg)` |
| nhats\_traj.dec\_arr | `Return Declination (deg)` |
| nhats_traj.tid | `NHATS Trajectory Solution ID` |

* * *

Change Log
----------

### Version 1.3 (2019 September)

* Updated launch range from `2015-2040` to `2020-2045`.

### Version 1.2 (2017 March)

* Obsolete plot URL output field `plot` has been removed. Use `plot_base64` instead.

### Version 1.1 (2017 February)

* Plot data file contents (base64-encoded) are now optionally available in field `plot_base64`.
* Prior plot URL output field `plot` is obsolete and will been removed soon (due to the URL source being removed).
* removed extraneous fields from `min_dv_traj` and `min_dur_traj`

### Version 1.0 (2016 August)

* Initial release