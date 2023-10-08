Three-Body Periodic Orbits API
==============================

**Version: 1.0** (2020 June)  
[change log](#change_log)

This API provides access to the Poincare Catalog of Periodic Orbits.

HTTP Request
------------

GET `https://ssd-api.jpl.nasa.gov/periodic_orbits.api`

Example Queries
---------------

* `https://ssd-api.jpl.nasa.gov/periodic_orbits.api?sys=earth-moon&family=halo&libr=1&branch=N` \- request the family of northern Halo orbits about L1 in the Earth-Moon system.
* `https://ssd-api.jpl.nasa.gov/periodic_orbits.api?sys=jupiter-europa&family=dro` \- request the family of distant retrograde orbits (DROs) about Europa in the Jupiter-Europa system.
* `https://ssd-api.jpl.nasa.gov/periodic_orbits.api?sys=saturn-enceladus&family=vertical&libr=2&periodmax=1&periodunits=d` \- request the Vertical orbits about L2 with period less than one day in the Saturn-Enceladus system.
* `https://ssd-api.jpl.nasa.gov/periodic_orbits.api?sys=saturn-titan&family=butterfly&jacobimin=3&stabmax=1&branch=N` \- request the Butterfly orbits with Jacobi constant greater or equal than 3 and stability index less or equal than 1 (stable) in the Saturn-Titan system.
* `https://ssd-api.jpl.nasa.gov/periodic_orbits.api?sys=mars-phobos&family=resonant&jacobimin=3&stabmax=1&branch=21` \- request the family of 2:1 planar resonant orbits in the Mars-Phobos system.

Query Parameters
----------------

Queries must always specify the system (`sys`) and the orbit family (`family`). Depending on the family, the branch (`branch`) or the libration point (`libr`) may also be required.

| Parameter | Type | Allowable Values | Default | Description |
| --- | --- | --- | --- | --- |
| `sys` | string |     |     | three-body system defined in lower-case as “primary-secondary,” e.g. `earth-moon`, `mars-phobos`, `sun-earth`. |
| `family` | string |     |     | name of the orbit family: `halo`,`vertical`,`axial`,`lyapunov`,`longp`,`short`,`butterfly`,`dragonfly`,`resonant`,`dro`,`dpo`,`lpo`. |
| `libr` | integer | `1`,`2`,`3`,`4`,`5` |     | libration point. Required for `lyapunov`,`halo` (`1`,`2`,`3`), `longp`, `short` (`4`,`5`), and `axial`,`vertical` (`1`,`2`,`3`,`4`,`5`). |
| `branch` | string |     |     | branch of orbits within the family: `N`/`S` for `halo`,`dragonfly`,`butterfly`, `E`/`W` for `lpo`, and _pq_ integer sequence for `resonant` (e.g., `12` for 1:2). |
| `periodmin` | float | (positive) |     | minimum period (inclusive). Units defined by `periodunits`. |
| `periodmax` | float | (positive) |     | maximum period (inclusive). Units defined by `periodunits`. |
| `periodunits` | string | `s`,`h`,`d`,`TU` | `TU` | units of `periodmin` and `periodmax`: `s` for seconds, `h` for hours, `d` for days, `TU` for nondimensional. |
| `jacobimin` | float |     |     | minimum Jacobi constant (inclusive). Nondimensional units. |
| `jacobimax` | float |     |     | maximum Jacobi constant (inclusive). Nondimensional units. |
| `stabmin` | float | (positive) |     | minimum stability index (inclusive). |
| `stabmax` | float | (positive) |     | maximum stability index (inclusive). |

The units of length and time used to normalize variables are the distance between the primaries and the inverse of the angular velocity of the system, respectively.

Data Output
-----------

> Please **always check** the JSON payload “signature” object for the API “version”. If the version does not match the version in this document (at the top), there is no guarantee that the format has not changed.
> 
> Example "signature" object with "version" value "1.0": `"signature":{"version":"1.0","source":"NASA/JPL ... API"}`

Data are returned in JSON format. The JSON payload contains the following fields:

* `signature` \- API version.
* `system` \- information about the three-body system:
    * `name` \- name of the system.
    * `mass_ratio` \- mass ratio of the primaries, m2 / (m1 + m2).
    * `radius_secondary` \- radius of the secondary (km).
    * `L1`, `L2`, `L3`, `L4`, `L5` \- coordinates of the libration points in the rotating frame (normalized units) in the synodic frame centered at the system’s barycenter.
    * `lunit` \- length unit in km (distance between the primaries).
    * `tunit` \- time unit in seconds (inverse of the angular frequency of the system).
* `family` \- requested family of periodic orbits.
* `libration_point` \- requested libration point.
* `branch` \- requested branch of the family `family`.
* `filter` \- parameters used to filter the orbits from the requested family:
    * `periodmin` \- minimum period (inclusive).
    * `periodmax` \- maximum period (inclusive).
    * `periodunits` \- units of `periodmin` and `periodmax`.
    * `jacobimin` \- minimum Jacobi constant (inclusive).
    * `jacobimax` \- maximum Jacobi constant (inclusive).
    * `stabmin` \- minimum stability index (inclusive).
    * `stabmax` \- maximum stability index (inclusive).
* `count` \- total number of matching records.
* `limits` \- minimum and maximum values of the Jacobi constant, period, and stability index for the requested orbits:
    * `jacobi` \- min and max values of the Jacobi constant for the current subset of orbits (normalized) \[min, max\]
    * `period` \- min and max values of the period for the current subset of orbits (normalized) \[min, max\]
    * `stability` \- min and max values of the stability index for the current subset of orbits \[min, max\]
* `fields` \- header of the `data` table.
* `data` \- table of periodic orbits. Each column is defined by the corresponding entry in `fields`:
    * `x`, `y`, `z`, `vx`, `vy`, `vz` \- initial conditions of the periodic orbit in the synodic frame. This is a rotating frame centered at the barycenter of the system with the X-axis pointing towards the secondary, the Z-axis defined by the angular velocity vector, and the Y-axis completing an orthogonal dextral reference frame.
    * `jacobi` \- normalized Jacobi constant.
    * `period` \- normalized period of the periodic orbit.
    * `stability` \- stability index. Orbital stability requires the stability index to be less than or equal to one.

The example query `https://ssd-api.jpl.nasa.gov/periodic_orbits.api?sys=earth-moon&family=halo&libr=1&branch=N` returns:

    {
      "signature" : {"source" : "NASA/JPL Three-Body Periodic Orbits API","version" : "1.0"},
      "system" : {
        "name" : "Earth-Moon",
        "mass_ratio" : "1.215058560962404e-02",
        "radius_secondary" : "1737.1",
        "L1" : ["0.8369151257723572","0.0","0.0"],
        "L2" : ["1.155682165444884","0.0","0.0"]
        "L3" : ["-1.005062645810278","0.0","0.0"],
        "L4" : ["0.4878494143903759","0.8660254037844386","0.0"],
        "L5" : ["0.4878494143903759","-0.8660254037844386","0.0"],
        "lunit" : "389703.2648292776",
        "tunit" : "382981.2891290545"
      },
      "family" : "halo",
      "libration_point" : "1",
      "branch" : "N",
      "limits" : {
        "jacobi" : ["0.19516273085815472","3.1743435193301153"],
        "period" : [" 1.8036720655626510E+00"," 3.1233143922761588E+00"],
        "stability" : ["1","1180.4065333857613"]
      },
      "count" : "5731",
      "fields" : ["x","y","z","vx","vy","vz","jacobi","period","stability"],
      "data" : [
        ["-4.1456184803140111E-01","0.0000000000000000E+00","9.0753120433295065E-01","-1.1555581216266303E-12","1.4076145460136695E+00","3.9979936124406781E-13","1.9516273085815472E-01","3.1233143922761588E+00","2.4340572681337468E+02"],
        ["-4.1421982661362478E-01","0.0000000000000000E+00","9.0768629637651521E-01","-1.1474877439509793E-12","1.4072700950580586E+00","3.9684610255625016E-13","1.9584418854987273E-01","3.1233112610554632E+00","2.4352872940755927E+02"],
        ["-4.1387776145545296E-01","0.0000000000000000E+00","9.0784124892510798E-01","-1.2473183104559711E-12","1.4069256037546980E+00","4.3182616480665382E-13","1.9652572760226936E-01","3.1233081181627282E+00","2.4365166172846071E+02"],
        ...
      ]
    }
    

> NOTE: the array presented in this example JSON file has been reduced for convenience.

The example query `https://ssd-api.jpl.nasa.gov/periodic_orbits.api?sys=saturn-titan&family=butterfly&jacobimin=3&stabmax=1&branch=N` returns:

    {
      "signature" : {"source" : "NASA/JPL Three-Body Periodic Orbits API","version" : "1.0"},
      "system" : {
        "name" : "Saturn-Titan",
        "mass_ratio" : "2.366393158331484e-04",
        "radius_secondary" : "2574.7",
        "L1" : ["0.9574961733241143","0.0","0.0"],
        "L2" : ["1.043256421347392","0.0","0.0"],
        "L3" : ["-1.00009859971421","0.0","0.0"]
        "L4" : ["0.4997633606841668","0.8660254037844386","0.0"],
        "L5" : ["0.4997633606841668","-0.8660254037844386","0.0"],
        "lunit" : "1195677.151917583",
        "tunit" : "212238.2726842311"
      },
      "family" : "butterfly",
      "branch" : "N",
      "limits" : {
          "jacobi" : ["3.009185830091419","3.009185830091419"],
          "period" : [" 3.6919029518069890E+00"," 3.6919029518069890E+00"],
          "stability" : ["1","1"]
      },
      "filter" : {
        "jacobimin" : "3",
        "periodunits" : "TU",
        "stabmax" : "1"
      },
      "count" : "1",
      "fields" : ["x","y","z","vx","vy","vz","jacobi","period","stability"],
      "data" : [
        ["9.7753007347844856E-01","0.0000000000000000E+00","3.9522030056289147E-02","4.9658541968180441E-14","-1.1884747036685137E-02","7.2136225765117000E-14","3.0091858300914192E+00","3.6919029518069890E+00","1.0000000000000000E+00"]
      ]
    }
    

HTTP Response Codes
-------------------

All errors are returned via appropriate HTTP response codes. Note that it is possible to submit query parameters resulting in no matching data. In such cases, a non-error code of 200 is returned so the user is responsible for checking the payload if they wish to detect a null-result.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal successful result: data returned (may be empty) or an error message (see [API internal exceptions](#exceptions)) |
| 400 | Bad Request | the request contained invalid keywords and/or content (details returned in the JSON payload) |
| 405 | Method Not Allowed | the request used an incorrect method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | the database is not available at the time of the request |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

### API Internal Exceptions

Normal returns with HTTP code 200 might include error messages or warnings related to special exceptions:

* `{ "warning" : "No matching records" }` \- no orbits were founds based on the requested parameters (`count` equals 0).

Change Log
----------

### Version 1.0 (2020 June)

* Initial release