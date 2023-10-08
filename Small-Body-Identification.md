Small-Body Identification API
=============================

**Version: 1.1** (2021 December)  
[change log](#change_log)

The Small-Body Identification API provides a method for finding known small bodies contained within a specified field of view (FOV) with respect to an observer location at a specified time.

HTTP Request
------------

GET `https://ssd-api.jpl.nasa.gov/sb_ident.api`

Example Query URLs
------------------

* Request the list of asteroids contained in the FOV defined by RA limits 10:10:10-10:20:00 and declination limits 10 00’00”-10 30’00” as seen from Mauna Kea observatory (568) at 2021-02-09 00:00:00 - `https://ssd-api.jpl.nasa.gov/sb_ident.api?sb-kind=a&mpc-code=568&obs-time=2021-02-09_00:00:00&mag-required=true&two-pass=true&suppress-first-pass=true&req-elem=false&vmag-lim=20&fov-ra-lim=10-10-00%2C10-20-00&fov-dec-lim=10-00-00,10-30-00`
    
* Request first-pass data (based on simplified two-body propagation) from observatory F51 (Haleakala), NEO only - `https://ssd-api.jpl.nasa.gov/sb_ident.api?mpc-code=F51&obs-time=2020-01-01T11:10:01&fov-ra-center=10-10-10&fov-dec-lim=10-05-10,10-10-10&two-pass=false&req-elem=false&sb-group=neo`
    
* Request osculating orbital elements of objects identified by second pass (numerical propagation using realistic force model) given the explicit geocentric state of the observer - `https://ssd-api.jpl.nasa.gov/sb_ident.api?xobs=-5.957613176404512E%2B02,-6.319860760764293E%2B03,2.735882778341472E%2B03,7.326499360212761E%2B00,1.801234666741493E-01,2.003177731007209E%2B00&obs-time=2020-12-14&fov-ra-center=03-00-00&fov-dec-center=20-00-00&fov-ra-hwidth=1.5&fov-dec-hwidth=1.5&two-pass=true&suppress-first-pass=true&req-elem=true`
    

Query Parameters
----------------

### Observer Location Parameters

\[**Required**\] The observer location must be specified using one of the methods described below.

You may specify the observer location on the Earth (topocentric) using either an MPC observatory code, geodetic coordinates (latitude, longitude, and altitude) or the parallax. For spacecraft, you may specify the geocentric or heliocentric state vector of the observer.

Location defined by MPC observatory code:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| mpc-code | string | _none_ | MPC observer location code |

Location defined by geodetic coordinates:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| lat | number | _none_ | geodetic latitude of observer (degrees, north-postive, \[-90, 90\]) |
| lon | number | _none_ | geodetic latitude of observer (degrees, east-postive, \[-180, 180\]) |
| alt | number | `0` | altitude above the reference ellipsoid {WGS-84/GPS} (km) |

Location defined by parallax:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| lon | number | _none_ | geodetic latitude of observer (degrees, east-postive, \[-180, 180\]) |
| dxy | number | _none_ | parallax constant perp. to Earth’s spin axis (au x 10^7) |
| dz  | number | _none_ | parallax constant along Earth’s spin axis (au x 10^7) |

Location defined by geocentric state vector:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| xobs | string | _none_ | geocentric position vector (km) and, optionally, velocity vector (km/s) of the observer in the equatorial J2000 frame. Fields separated by commas. If the observer’s velocity is not specified, the API will not return the object’s rate of motion. |

Location defined by heliocentric state vector:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| xobs-hel | string | _none_ | heliocentric position vector (au) and, optionally, velocity vector (au/d) of the observer in the equatorial J2000 frame. Fields separated by commas. If the observer’s velocity is not specified, the API will not return the object’s rate of motion. |

### Observation Constraint Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| obs-time | string | _none_ | \[**required**\] date/time of the observing night (formatted as `YYYY-MM-DD[_hh:mm:ss]` or JD, e.g., `2459555.5`) |
| vmag-lim | number | _none_ | visual magnitude threshold |

### Field of View Parameters

\[**Required**\] The FOV must be defined using one of the two methods described below.

FOV edges defined explicitly:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| fov-ra-lim | string | _none_ | right-ascension values of the edges of the FOV (hh-mm-ss\[.ss\]). Fields separated by commas. For negative values, replace minus sign with `M`, e.g. `M10-00-00.02` |
| fov-dec-lim | string | _none_ | declination values of the edges of the FOV (dd-mm-ss\[.ss\]). Fields separated by commas. For negative values, replace minus sign with `M`, e.g. `M10-00-00.02` |

FOV defined by center and half-width:

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| fov-ra-center | string | _none_ | right-ascension of the center of the FOV (hh-mm-ss\[.ss\]). For negative values, replace minus sign with `M`, e.g. `M10-00-00.02` |
| fov-dec-center | string | _none_ | declination of the center of the FOV (dd-mm-ss\[.ss\]). For negative values, replace minus sign with `M`, e.g. `M10-00-00.02` |
| fov-ra-hwidth | number | 0.5 | half-width of the FOV along RA (degrees) |
| fov-dec-hwidth | number | 0.5 | half-width of the FOV along DEC (degrees) |

### Filtering Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| two-pass | boolean | `false` | request a second pass that numerically integrates the orbits using a high-fidelity force model instead of using a two-body model |
| mag-required | boolean | `true` | skip objects without magnitude parameters |
| suppress-first-pass | boolean | `true` | (only used if `two-pass` is true) suppress output from first pass, which assumes two-body orbits |

### Output/Format Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| req-elem | boolean | `false` | request osculating orbital elements of matching objects |

### SBDB Filter Constraints

Additional constraints on the orbital and physical parameters of the objects are available using the [SBDB Filter](sbdb_filter.html).

### Required Parameters

* observer (observer location) is specified using one of the following forms:
    * valid topocentric MPC 3-character code (e.g., `F51` but never `500` ‘geocentric’), or
    * geodetic coordinates: latitude, longitude, and altitude
        * latitude (`lat`) is specified in decimal degrees (north positive)
        * longitude (`lon`) is specified in decimal degrees (east positive)
        * altitude (`alt`) is specified in decimal kilometers (above the reference ellipsoid {WGS-84/GPS})
    * parallax:
        * geodetic latitude (`lon`) of observer (degrees, east-postive)
        * parallax constant perp. to Earth’s spin axis (`dxy`, au x 10^7)
        * parallax constant along Earth’s spin axis (`dz`, au x 10^7)
    * geocentric state vector:
        * `xobs` \- components of the position and velocity vector w.r.t. Earth in the equatorial J2000 frame (km and km/s).
    * heliocentric state vector:
        * `xobs-hel` \- components of the position and velocity vector w.r.t. the Sun in the equatorial J2000 frame (au and au/d).
* obs-time (date/time of observation)
    * this date/time is used to determine the corresponding night at the specified observer location
    * time (hh:mm:ss) is not necessary in most cases although it may be convenient to specify hours to ensure selection of the desired night

### NEO-Only Mode

To request only NEOs, add the field `sb-group=neo` to the query URL.

Data Output
-----------

> Please **always check** the JSON payload “signature” object for the API “version”. If the version does not match the version in this document (at the top), there is no guarantee that the format has not changed.
> 
> Example "signature" object with "version" value "1.0": `"signature":{"version":"1.0","source":"NASA/JPL ... API"}`

A successful run of the SB_IDENT code results in details related to the observer location and FOV and a table of objects contained within the specified FOV.

### Data Structure

* `summary` \- list of user-defined variables in the current API call.
* `observer` \- observation summary data. May contain the following fields:
    * `obs_date` \- date of observation
    * `location` \- name of observatory (only if `mpc-code` is defined).
    * `fov_center` \- right-ascension and declination of the astrometric center of the FOV (hh:mm:ss, dd mm’ss”).
    * `fov_offset` \- right-ascension and declination offset of the FOV (hh:mm:ss, dd mm’ss”).
* `n_first_pass` \- number of records from first pass.
* `n_second_pass` \- number of records from second pass (only if `two-pass` is true).
* `fields_first` \- columns headers for `data_first_pass`.
* `fields_second` \- columns headers for `data_second_pass`.

If `req-elem` is false:

* `data_first_pass` \- data table from first pass (unless `suppres-first-pass` is true). Column headers are defined by `fields_first`.
* `data_second_pass` \- data table from second pass (only if `two-pass` is true). Column headers are defined by `fields_second`.

If `req-elem` is true:

* `elem_first_pass` \- table of osculating orbital elements of objects found in the first pass (unless `suppres-first-pass` is true). Column headers are defined by `fields_first`.
* `elem_second_pass` \- table of osculating orbital elements of objects found in the second pass (only if `two-pass` is true). Column headers are defined by `fields_second`.

If additional SBDB Filter constraints are present:

* `sb_constraints` \- summary of SBDB Filter constraints.

### Example Output

Request the list of asteroids contained in the FOV defined by RA limits 10:10:10-10:20:00 and declination limits 10 00’00”-10 30’00” as seen from Mauna Kea observatory (568) at 2021-02-09 00:00:00

API call - `https://ssd-api.jpl.nasa.gov/sb_ident.api?sb-kind=a&mpc-code=568&obs-time=2021-02-09_00:00:00&mag-required=true&two-pass=true&suppress-first-pass=true&req-elem=false&vmag-lim=20&fov-ra-lim=10-10-00%2C10-20-00&fov-dec-lim=10-00-00,10-30-00`

API output:

    {
       "signature" : {
          "source" : "NASA/JPL Small-Body Identification API",
          "version" : "1.1"
       },
       "summary" : {
          "fov-dec-lim" : "10-00-00,10-30-00",
          "fov-ra-lim" : "10-10-00,10-20-00",
          "mag-required" : "true",
          "mpc-code" : "568",
          "obs-time" : "2021-02-09 00:00:00",
          "req-elem" : "false",
          "suppress-first-pass" : "true",
          "two-pass" : "true",
          "vmag-lim" : "20"
       },
       "n_second_pass" : 20,
       "observer" : {
          "fov_center" : "10:15:00.00, +10 15'00.00\" (J2000)",
          "fov_offset" : "00:05:00.00, +00 15'00.00\"",
          "location" : "Mauna Kea",
          "obs_date" : "2021-Feb-09 00:00:00 (2459254.500000 UT)"
       },
       "sb_constraints" : {
          "sb-kind" : "a"
       },
       "fields_second" : [
          "Object name",
          "Astrometric RA (hh:mm:ss)",
          "Astrometric Dec (dd mm'ss\")",
          "Dist. from center RA (\")",
          "Dist. from center Dec (\")",
          "Dist. from center Norm (\")",
          "Visual magnitude (V)",
          "RA rate (deg/s)",
          "Dec rate (deg/s)"
       ],
       "data_second_pass" : [
          ["10193 Nishimoto (1996 PR1)","10:16:58.22","+10 28'34.3\"","2.E3","814.","1951.","19.4","-2.564E+01","1.035E+01"],
          ["15319 (1993 NU1)","10:19:52.66","+10 06'12.0\"","4.E3","-528.","4422.","18.5","-3.293E+01","1.591E+01"],
          ...
        ]
    }
    

Request first-pass data from observatory F51 (Haleakala), NEO only.

API call - `https://ssd-api.jpl.nasa.gov/sb_ident.api?mpc-code=F51&obs-time=2020-01-01T11:10:01&fov-ra-center=10-10-10&fov-dec-lim=10-05-10,10-10-10&two-pass=false&req-elem=false&sb-group=neo`

API output:

    {
       "signature" : {
          "source" : "NASA/JPL Small-Body Identification API",
          "version" : "1.1"
       },
       "summary" : {
          "fov-dec-lim" : "10-05-10,10-10-10",
          "fov-ra-center" : "10-10-10",
          "fov-ra-hwidth" : "30",
          "mpc-code" : "F51",
          "obs-time" : "2020-01-01 11:10:01",
          "req-elem" : "false",
          "two-pass" : "false"
       },
       "n_first_pass" : 1013,
       "observer" : {
          "fov_center" : "10:10:10.00, +10 07'40.00\" (J2000)",
          "fov_offset" : "02:00:00.00, +00 02'30.00\"",
          "location" : "Pan-STARRS 1, Haleakala",
          "obs_date" : "2020-Jan-01 11:10:01 (2458849.965289 UT)"
       },
       "sb_constraints" : {
          "sb-group" : "neo"
       },
       "fields_first" : [
          "Object name",
          "Astrometric RA (hh:mm:ss)",
          "Astrometric Dec (dd mm'ss\")",
          "Dist. from center RA (\")",
          "Dist. from center Dec (\")",
          "Dist. from center Norm (\")",
          "Visual magnitude (V)",
          "RA rate (deg/s)",
          "Dec rate (deg/s)",
          "Est. error RA (\")",
          "Est. error Dec (\")"
       ],
       "data_first_pass" : [
          ["6489 Golevka (1991 JX)","02:00:07","+11 11'53\"","-4.E5","4.E3","4.4E5","24.1","1.107E+01","3.717E+00","5.4E5","5.4E5"],
          ["194006 (2001 SG10)","08:55:26","+10 05'19\"","-7.E4","-141.","6.7E4","23.1","-4.233E+01","6.650E+00","141.1","141.1"],
          ...
       ]
    }
    

Request osculating orbital elements of objects identified by second pass given the explicit geocentric state of the observer.

API call - `https://ssd-api.jpl.nasa.gov/sb_ident.api?xobs=-5.957613176404512E%2B02,-6.319860760764293E%2B03,2.735882778341472E%2B03,7.326499360212761E%2B00,1.801234666741493E-01,2.003177731007209E%2B00&obs-time=2020-12-14&fov-ra-center=03-00-00&fov-dec-center=20-00-00&fov-ra-hwidth=1.5&fov-dec-hwidth=1.5&two-pass=true&suppress-first-pass=true&req-elem=true`

API output:

    {
       "signature" : {
          "source" : "NASA/JPL Small-Body Identification API",
          "version" : "1.1"
       },
       "summary" : {
          "fov-dec-center" : "20-00-00",
          "fov-dec-hwidth" : "1.5",
          "fov-ra-center" : "03-00-00",
          "fov-ra-hwidth" : "1.5",
          "obs-time" : "2020-12-14",
          "req-elem" : "true",
          "suppress-first-pass" : "true",
          "two-pass" : "true",
          "xobs" : "-5.957613176404512E+02,-6.319860760764293E+03,2.735882778341472E+03,7.326499360212761E+00,1.801234666741493E-01,2.003177731007209E+00"
       },
       "n_second_pass" : 519,
       "observer" : {
          "fov_center" : "03:00:00.00, +20 00'00.00\" (J2000)",
          "fov_offset" : "00:06:00.00, +01 30'00.00\"",
          "frame" : "J2000",
          "obs_date" : "2020-Dec-14 00:00:00 (2459197.500000 UT)"
       },
       "fields_second" : [
          "Object name",
          "Absolute magntiude (H)",
          "Magnitude slope (G)",
          "Eccentricity",
          "Perihelion (au)",
          "Time of perihelion passage (JD)",
          "Longitude of ascending node (deg)",
          "Argument of perihelion (deg)",
          "Inclination (deg)",
          "Epoch (JD)"
       ],
       "elem_second_pass" : [
          ["177 Irma","9.60","0.15","0.233641161","2.1234681","2459092.45216","346.97786","39.355511","1.3822545","2459000.5"],
          ["523 Ada (A904 BA)","9.90","0.15","0.178529043","2.4359491","2459306.32142","260.61929","189.98370","4.3131271","2459000.5"],
          ...
       ]
    }
    

HTTP Response Codes
-------------------

All errors are returned via appropriate HTTP response codes.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal successful result |
| 400 | Bad Request | the request contained invalid keywords, content, or results are invalid: details returned |
| 405 | Method Not Allowed | the request used an incorrect method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | the database is not available at the time of the request |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

Change Log
----------

### Version 1.1 (2021 December)

* `obs-time`: allow JD as well as calendar date/time
* bug fix: non-topocentric locations were using an incorrectly rounded time

### Version 1.0 (2021 March)

* Initial release