Small-Body Mission Design API
=============================

**Version: 1.1** (2021 March)  
[change log](#change_log)

This API provides access to the JPL/SSD small-body mission design suite. The following operation modes are available:

* Mode `A` (accessible) - retrieves the list of accessible small bodies based on user-defined constraint.
* Mode `Q` (query) - retrieves pre-computed mission options to a specific object. Both impulsive and low-thrust gravity-assist mission options are available.
* Mode `M` (map) - an extension of mode `Q` for the impulsive case, returns the data required to render a porkchop plot with multiple parameters.
* Mode `T` (mission extension) - retrieves the list of small bodies that come closest (or within a prescribed distance) to a user-defined orbit during a certain period of time. This is a crude filter for finding potential candidates for mission extensions.

HTTP Request
------------

GET `https://ssd-api.jpl.nasa.gov/mdesign.api`

Example Queries
---------------

* Mode `A` \- Request the list of accessible small bodies given a set of user-defined constraints. Both ballistic and low-thrust gravity-assist options are avaiable.

`https://ssd-api.jpl.nasa.gov/mdesign.api?lim=200&crit=1&year=2025,2026,2027,2028,2029&sb-group=neo` \- (Ballistic). List of accessible NEOs sorted by departure V-infinity. Launch period spans from 2025 through 2029. Limited to the top 200 matches.

`https://ssd-api.jpl.nasa.gov/mdesign.api?lim=200&crit=3&year=2032&sb-class=MBA&sb-sat=true` \- (Ballistic). List of accessible binary main-belt asteroids sorted by total delta-V. Launch in 2032. Limited to the top 200 matches.

`https://ssd-api.jpl.nasa.gov/mdesign.api?lim=200&rdzvs=true&profile=1&year=2025,2026,2027,2028,2029&sb-class=TJN` \- (Low-thrust). List of rendezvous opportunities with Trojan asteroids for a mid-size spacecraft. Launch period spans from 2025 through 2029. Limited to the top 200 matches.

* Mode `Q` \- Given an object identifier, return the pre-computed mission options stored in the Small-Body Database. Both ballistic and low-thrust gravity-assist options are available.

`https://ssd-api.jpl.nasa.gov/mdesign.api?des=2012%20TC4` \- Ballistic missions to asteroid 2012 TC4

`https://ssd-api.jpl.nasa.gov/mdesign.api?des=1&class=true` \- Ballistic missions to asteroid 1 Ceres

`https://ssd-api.jpl.nasa.gov/mdesign.api?sstr=apophis` \- Ballistic missions to asteroid 99942 Apophis

`https://ssd-api.jpl.nasa.gov/mdesign.api?sstr=65803&ltdata=true` \- Low-thrust gravity-assist mission opportunities to reach asteroid 65803 Didymos.

* Mode `M` \- In addition to querying the database like in mode `Q` (ballistic), compute all ballistic mission options to the specified target within certain ranges of launch dates and times of flight.

`https://ssd-api.jpl.nasa.gov/mdesign.api?des=2012%20TC4&mjd0=58490&span=3652&tof-min=10&tof-max=365&step=2` \- Missions to asteroid 2012 TC4

`https://ssd-api.jpl.nasa.gov/mdesign.api?des=1&mjd0=59000&span=1800&tof-min=120&tof-max=1500&step=5` \- Missions to 1 Ceres

* Mode `T` \- Request list of small bodies that come closest (or within a prescribed distance) to a user-specified heliocentric orbit (assumes two-body dynamics). Proxy for easiest-to-reach targets for an extended mission phase.

`https://ssd-api.jpl.nasa.gov/mdesign.api?ec=0.2056408220896557&qr=0.3074958016246215&tp=2459067.6508400026&om=48.30597718083336&w=29.18348714438387&in=7.003733902930839&jd0=2458849.5&jdf=2459132.5&maxout=100&maxdist=0.0010` \- Request the 100 objects that come within 0.001 au of a user-specified orbit between 2020-1-1 and 2020-10-10.

Query Parameters
----------------

### Mode `A`

If ballistic missions are requested, the API expects `crit` to be defined. If low-thrust missions are requested, the API expects `profile` and `rdzvs` to be defined.

| Parameter | Type | Allowable Values | Default | Modes | Description |
| --- | --- | --- | --- | --- | --- |
| `lim` | int |     | 200 | `A` | number of records to be retrieved form the SBDB |
| `crit` | int | \[1,6\] | 1   | `A` | optimality criterion for selecting ballistic missions: 1) min. departure V-infinity, 2) min. arrival V-infinity, 3) min. total delta-V, 4) min. tof + min. departure V-infinity, 5) min. tof + min. arrival V-infinity, 6) min. tof + min. total delta-V |
| `year` | int | current year + \[0, 20\] | current year + \[0,4\] | `A` | launch year or list of launch years for which optimal missions are to be retrieved from the SBDB |
| `rdzvs` | boolean |     | `true` | `A` | when requesting low-thrust missions, if `rdzvs` is true, only rendezvous missions are retrieved from the SBDB. If false, flyby missions will be retrieved |
| `profile` | int | 1,2 | 1   | `A` | when requesting low-thrust missions, `profile` maps to the spacecraft configuration: 1) Mid-size spacecraft, 2) smallsat |

### Mode `Q`

The target body must be specified via `des`, `spk`, or `sstr`.

| Parameter | Type | Allowable Values | Default | Modes | Description |
| --- | --- | --- | --- | --- | --- |
| `des` | string |     |     | `Q`,`M` | designation (provisional or IAU-number) of the desired object (e.g., `2015 AB` or `141P` or `433`). NOTE: when submitting a `des` containing a space in your query string, you must replace the space with `%20`, for example `2015%20AB`. Do not use `des` if `spk` or `sstr` are specified. |
| `spk` | int |     |     | `Q`,`M` | SPK-ID of the desired object (e.g., `2000433`). Do not use `spk` if `des` or `sstr` are specified. |
| `sstr` | string |     |     | `Q`,`M` | object search string: designation in various forms (including MPC packed form), case-insensitive name, or SPK-ID; designation can be an alternate provisional designation; examples: `atira`, `2003 CP20`, `2003cp20`, `K03C20P`, `163693`, `2163693`. Do not use `sstr` if `des` or `spk` are specified. |
| `class` | boolean |     | `false` | `Q`,`M` | if set to _true_, return the orbit class in human-readable format instead of the three-letter code (default _false_). |
| `ltdata` | boolean |     | `false` | `Q`,`M` | if set to _true_, return the list of low-thrust gravity-assist mission opportunities. |

### Mode `M`

In addition to the object identifier, the user must specify the range of launch dates and times of flight to be explored by the search algorithm.

| Parameter | Type | Allowable Values | Default | Modes | Description |
| --- | --- | --- | --- | --- | --- |
| `des` | string |     |     | `Q`,`M` | designation (provisional or IAU-number) of the desired object (e.g., `2015 AB` or `141P` or `433`). NOTE: when submitting a `des` containing a space in your query string, you must replace the space with `%20`, for example `2015%20AB`. Do not use `des` if `spk` or `sstr` are specified. |
| `spk` | int |     |     | `Q`,`M` | SPK-ID of the desired object (e.g., `2000433`). Do not use `spk` if `des` or `sstr` are specified. |
| `sstr` | string |     |     | `Q`,`M` | object search string: designation in various forms (including MPC packed form), case-insensitive name, or SPK-ID; designation can be an alternate provisional designation; examples: `atira`, `2003 CP20`, `2003cp20`, `K03C20P`, `163693`, `2163693`. Do not use `sstr` if `des` or `spk` are specified. |
| `class` | boolean |     | `false` | `Q`,`M` | if set to _true_, return the orbit class in human-readable format instead of the three-letter code (default _false_). |
| `mjd0` | int | \[33282,73459\] |     | `M` | first launch date to be explored (Modified Julian Date) |
| `span` | int | \[10,9200\] |     | `M` | duration of the launch-date period to be explored (days) |
| `tof-min` | int | \[10,9200\] |     | `M` | minimum time of flight to be considered (days) |
| `tof-max` | int | \[10,9200\] |     | `M` | maximum time of flight to be considered (days) |
| `step` | int | 1,2,5,10,15,20,30 |     | `M` | time step used to advance both the launch date and the time of flight (days). The size of the transfer map is limited to 1,500,000 points. Consequently, if the requested configuration leads to too many points, the API will return with code 200 and an error message in the JSON payload. |

> NOTE: `Modified Julian Date (MJD) = JD - 2400000.5`

### Mode `T`

The reference heliocentric orbit is defined by providing the corresponding set of cometary elements.

| Parameter | Type | Allowable Values | Default | Modes | Description |
| --- | --- | --- | --- | --- | --- |
| `ec` | double | >=0 |     | `T` | eccentricity |
| `qr` | double | >=0 |     | `T` | perihelion distance |
| `tp` | double | valid JD date |     | `T` | time of perihelion passage (JD) |
| `in` | double | \[0, 180\] |     | `T` | inclination (deg) |
| `om` | double | \[0, 360\] |     | `T` | longitude of the ascending node (deg) |
| `w` | double | \[0, 360\] |     | `T` | argument of periapsis (deg) |
| `jd0` | double | valid JD date |     | `T` | beginning of the requested time span (JD) |
| `jdf` | double | valid JD date |     | `T` | end of the requested time span (JD). Time span must not be longer than one year |
| `maxout` | int | >0  |     | `T` | maximum number of records to be returned |
| `maxdist` | double | >0  |     | `T` | _(optional)_ ignore objects with distance of closest approach greater than `maxdist` |

### SBDB Filter Constraints

Additional constraints on the orbital and physical parameters of the objects are available using the [SBDB Filter](sbdb_filter.html).

Data Output
-----------

> Please **always check** the JSON payload “signature” object for the API “version”. If the version does not match the version in this document (at the top), there is no guarantee that the format has not changed.
> 
> Example "signature" object with "version" value "1.0": `"signature":{"version":"1.0","source":"NASA/JPL ... API"}`

Data are returned in JSON format.

In modes `Q` and `M`, the JSON payload will always contain an object that presents information about the small body being considered. The attributes of this object are:

* `des` \- the primary designation of the object.
* `fullname` \- the full name of the object.
* `spkid` \- the primary SPK-ID of the object.
* `orbit_class` \- the orbit class of the object. If `class` is _false_, the orbit class is given by a three-letter code. If set to _true_, the full name of the orbit class is returned instead.
* `condition_code` \- condition code of the orbit solution: orbit uncertainty estimate 0-9, with 0 being good, and 9 being highly uncertain.
* `data_arc` \- number of days spanned by the data-arc the orbit solution was computed from.
* `orbit_id` \- the current orbit solution of the specified small body.
* `md_orbit_id` \- the orbit solution that was used to compute the data in the JSON payload. If `md_orbit_id` does not match `orbit_id` and the API is operating in mode `M`, the list of pre-computed missions might not be consistent with the pork-chop plot data. See [API internal exceptions](#exceptions) for more information about this exception.
* `computed_on` \- date on which the trajectories were computed.

### Mode `A`

In accessible mode `A`, the JSON payload contains the list of accessible targets with selected mission parameters and physical and orbital characteristics. In addition, a summary section with the active constraints applied in the query is also provided.

In **ballistic mode**, the columns of the table, listed in `fields`, are:

* `name` \- small-body full name.
* `date0` \- departure date (cal).
* `MJD0` \- departure date (MJD).
* `datef` \- arrival date (cal).
* `MJDF` \- arrival date (MJD).
* `c3_dep` \- departure characteristic energy C3 (km^2/s^2).
* `vinf_dep` \- departure V-infinity (km/s).
* `vinf_arr` \- arrival V-infinity (km/s).
* `dv_tot` \- total delta-V (km/s).
* `tof` \- time of flight (d).
* `class` \- three-letter orbit class code.
* `H` \- absolute magnitude.
* `condition_code` \- orbit condition code.
* `neo` \- Near-Earth Object flag.
* `pha` \- Potentially-Hazardous Asteroid flag.
* `bin` \- binary flag.
* `pdes` \- designation.

In **low-thrust gravity-assist**, the columns of the table, listed in `fields`, are:

* `name` \- small-body full name.
* `date0` \- departure date (cal).
* `MJD0` \- departure date (MJD).
* `datef` \- arrival date (cal).
* `MJDF` \- arrival date (MJD).
* `c3_dep` \- departure characteristic energy C3 (km^2/s^2).
* `vinf_dep` \- departure V-infinity (km/s).
* `vinf_arr` \- arrival V-infinity (km/s).
* `mass_dep` \- departure mass.
* `mass_arr` \- arrival mass.
* `tof` \- time of flight (d).
* `nga` \- number of Earth gravity assists.
* `rdzvs` \- rendezvous flag.
* `class` \- three-letter orbit class code.
* `H` \- absolute magnitude.
* `condition_code` \- orbit condition code.
* `neo` \- Near-Earth Object flag.
* `pha` \- Potentially-Hazardous Asteroid flag.
* `bin` \- binary flag.
* `pdes` \- designation.

In addition, the JSON payload contains an object `constraints` that summarizes the constraints of the query.

In **ballistic mode**, the JSON payload will be similar to:

    {
      "signature" : {"version":"1.1","source":"NASA/JPL Small-Body Mission Design API"},
      "fields": ["name","date0","MJD0","datef","MJDF","c3_dep","vinf_dep","vinf_arr","dv_tot","tof","class","H","condition_code","neo","pha","bin","pdes"],
      "constraints": {"lim":200,"year":"2025,2026,2027,2028,2029","crit":1}
      "records": [
        ["52768 (1998 OR2)","2026-09-11",61294,"2031-04-28",62984,0.00258064,0.0508,8.4057,8.4565,1690,"AMO",15.8,0,"Y","Y","","52768"],
        ["137108 (1999 AN10)","2027-04-19",61514,"2027-08-07",61624,0.00868624,0.0932,26.3335,26.4267,110,"APO",17.9,0,"Y","Y","","137108"],
        ["451124 (2009 KC3)","2027-12-15",61754,"2032-08-20",63464,0.01334025,0.1155,12.3224,12.4379,1710,"APO",18.0,1,"Y","Y","","451124"],
        ...
      ]
    }
    

In **low-thrust gravity-assist**, the JSON payload will be similar to:

    {
      "signature" : {"version":"1.1","source":"NASA/JPL Small-Body Mission Design API"},
      "fields": ["name","date0","MJD0","datef","MJDF","c3_dep","vinf_dep","vinf_arr","mass_dep","mass_arr","tof","nga","rdzvs","class","H","condition_code","neo","pha","bin","pdes"],
      "constraints": {"lim":200,"year":"2025,2026,2027,2028,2029","profile":1,"rdzvs":"true"}
      "records": [
        ["136617 (1994 CC)","2026-07-02",61223,"2028-06-24",61946,30,5.47722557505166,0,150,141.32,723,0,"Y","APO",17.7,0,"Y","Y","Y","136617"],
        ["153591 (2001 SN263)","2026-02-12",61083,"2030-08-02",62715,30.0001,5.47723470375335,0,150,140.836,1632,2,"Y","AMO",16.9,0,"Y","","Y","153591"],
        ["65803 Didymos (1996 GT)","2025-09-28",60946,"2028-10-20",62064,30,5.47722557505166,0,150,135.817,1118,1,"Y","APO",18.0,0,"Y","Y","Y","65803"],
        ...
      ]
    }
    

> NOTE: the arrays presented in this example JSON file have been reduced for convenience.

### Mode `Q`

The API will return the data that was pre-computed and stored in the Small Body Database:

* `fields` \- the table containing the list of pre-computed missions is provided in the form of a 2D array. `fields` is simply the header of this table.

In **ballistic mode**, the columns of the table, listed in `fields`, are:

    * `MJD0` - departure date from Earth (Modified Julian Date).
    * `MJDf` - arrival date to target (Modified Julian Date).
    * `vinf_dep` - magnitude of the departure V-infinity vector (km/s).
    * `vinf_arr` - magnitude of the arrival V-infinity vector (km/s).
    * `phase_ang` - Sun phase angle: angle between the arrival V-infinity vector and Sun-target position vector at arrival (deg).
    * `earth_dist` - distance from the spacecraft to Earth at the date of arrival (au).
    * `elong_arr` - solar elongation at arrival (Sun-Earth-probe angle): apparent angle between the Sun and the target as seen from Earth at the date of arrival (deg).
    * `decl_dep` - declination of the outgoing V-infinity vector referred to the Earth's mean equator (deg).
    * `approach_ang` - approach angle to the target (pump angle): angle between the arrival V-infinity vector and the velocity vector of the target body (deg).
    

In **low-thrust gravity-assist** mode, the columns of the table, listed in `fields`, are:

    * `date0` - departure date (cal).
    * `MJD0` - departure date (MJD).
    * `datef` - arrival date (cal).
    * `MJDF` - arrival date (MJD).
    * `mass_dep` - departure mass (kg)
    * `mass_arr` - arrival mass (kg)
    * `c3_dep` - departure characteristic energy C3 (km^2/s^2).
    * `c3_arr` - arrival characteristic energy C3 (km^2/s^2).
    * `tof` - time of flight (d).
    * `rdzvs` - rendezvous flag.
    * `profile` - spacecraft configuration profile.
    * `nga` - number of Earth gravity assists.
    

* `selectedMissions` \- the 2D array containing the list of pre-computed trajectories stored in the Small Body Database. These solutions capture the _optimal_ launch windows (in terms of departure _C_3), as well as minimum time and minimum arrival V-infinity options under certain _C_3 constraints. Each row corresponds to a different mission, and the columns are defined by `fields`.

No additional information will be provided when operating in query mode (`Q`). It is possible that no feasible transfer options were found to the specified object. In that case, the JSON payload will include an error message (see [API internal exceptions](#exceptions)).

In **ballistic mode**, the JSON payload will be similar to:

    {
      "signature" : {"version":"1.1","source":"NASA/JPL Small-Body Mission Design API"},
      "object" : {
        "des" : "1",
        "fullname" : "1 Ceres",
        "spkid" : "2000001",
        "orbit_class" : "MBA",
        "condition_code" : "0",
        "data_arc" : "24437",
        "orbit_id" : "34",
        "md_orbit_id" : "34",
        "computed_on" : "2019-02-13"
      },
      "fields": ["MJD0","MJDf","vinf_dep","vinf_arr","phase_ang","earth_dist","elong_arr","decl_dep","approach_ang"],
      "selectedMissions": [[61745,62125,11.3777,6.3766,52.45,3.8475,4.58,5.98,137.61],
        [62265,62565,11.26,9.9863,26.18,3.9258,12.22,-69.43,116.36],
        [65535,65820,11.177,10.6303,25.75,3.8817,20.29,-69.69,112.90],
        [62580,64015,10.9125,6.7504,58.75,3.9083,15.97,-55.14,135.37],
        [58070,58320,10.8491,9.6527,33.45,3.2371,41.76,69.94,120.41],
        [66465,66705,10.8218,10.6407,29.94,3.1188,48.58,70.36,117.04],
        ...
      ]
    }
    

In **low-thrust gravity-assist**, the JSON payload will be similar to:

    {
      "signature" : {"version":"1.1","source":"NASA/JPL Small-Body Mission Design API"},
      "object" : {
        "des" : "136617",
        "fullname" : "136617 (1994 CC)",
        "spkid" : "2136617",
        "orbit_class" : "Apollo",
        "condition_code" : "0",
        "data_arc" : "11105",
        "orbit_id" : "159",
        "lt_orbit_id" : "159",
        "computed_on" : "2020-03-10"
      },
      "fields": ["date0","MJD0","datef","MJDF","mass_dep","mass_arr","c3_dep","c3_arr","tof","rdzvs","profile","nga"],
      "selectedMissions": [
        ["2024-04-04",60404,"2025-02-08",60714,150,149.998,1.95699,110.134,310,0,2,0],
        ["2026-03-08",61107,"2028-03-02",61832,150,147.788,30,278.912,725,0,2,0],
        ...
      ]
    }
    

> NOTE: the array `selectedMissions` in the example above has been reduced for convenience.

### Mode `M`

In map mode `M`, the JSON payload also includes several 2D arrays containing information for representing _time of flight vs departure date_ maps:

* `vinf_dep` \- magnitude of the departure V-infinity vector (km/s).
* `vinf_arr` \- magnitude of the arrival V-infinity vector (km/s).
* `phase_ang` \- Sun phase angle: angle between the arrival V-infinity vector and Sun-target position vector at arrival (deg).
* `earth_dist` \- distance from the spacecraft to Earth at the date of arrival (au).
* `elong_arr` \- solar elongation at arrival (Sun-Earth-probe angle): apparent angle between the Sun and the target as seen from Earth at the date of arrival (deg).
* `decl_dep` \- declination of the outgoing V-infinity vector referred to the Earth’s mean equator (deg).
* `approach_ang` \- approach angle to the target (pump angle): angle between the arrival V-infinity vector and the velocity vector of the target body (deg).

In addition, the values of the _x_-_y_ axes of the maps are also provided:

* `dep_date` \- departure dates from Earth (Modified Julian Date), corresponding to the _x_-axis.
* `tof` \- times of flight to the target (days), corresponding to the _y_-axis.

If `dep_date` has _m_ elements and `tof` has _n_, then the 2D arrays are of dimension _n_ x _m_.

    {
      "signature" : {"version":"1.1","source":"NASA/JPL Small-Body Mission Design API"},
      "object" : {
        "des" : "1",
        "fullname" : "1 Ceres",
        "spkid" : "2000001",
        "orbit_class" : "MBA",
        "condition_code" : "0",
        "data_arc" : "24437",
        "orbit_id" : "34",
        "md_orbit_id" : "34",
        "computed_on" : "2019-02-13"
      },
        "dv_lowthrust": {
        "sep": 3.893999531963410e+01,
        "const": 1.336244672239606e+01
      },
      "fields": ["MJD0","MJDf","vinf_dep","vinf_arr","phase_ang","earth_dist","elong_arr","decl_dep","approach_ang"],
      "selectedMissions": [[61745,62125,11.3777,6.3766,52.45,3.8475,4.58,5.98,137.61],
        [62265,62565,11.26,9.9863,26.18,3.9258,12.22,-69.43,116.36],
        [65535,65820,11.177,10.6303,25.75,3.8817,20.29,-69.69,112.90],
        [62580,64015,10.9125,6.7504,58.75,3.9083,15.97,-55.14,135.37],
        [58070,58320,10.8491,9.6527,33.45,3.2371,41.76,69.94,120.41],
        [66465,66705,10.8218,10.6407,29.94,3.1188,48.58,70.36,117.04],
        ...
      ],
      "dep_date": [58022.0,58032.0,58042.0,58052.0,58062.0,58072.0,58082.0,58092.0,58102.0,58112.0,58122.0,58132.0,58142.0,...],
      "tof": [1.2000E+02,1.3000E+02,1.4000E+02,1.5000E+02,1.6000E+02,1.7000E+02,1.8000E+02,1.9000E+02,2.0000E+02,...],
      "vinf_dep": [[2.850E+01,2.602E+01,2.360E+01,2.139E+01,1.957E+01,1.829E+01,...],
        [2.569E+01,2.340E+01,2.117E+01,1.916E+01,1.757E+01,1.653E+01,1.631E+01,...],
        [2.339E+01,2.124E+01,1.918E+01,1.736E+01,1.597E+01,1.515E+01,1.514E+01,...],
        [2.150E+01,1.947E+01,1.755E+01,1.590E+01,1.469E+01,1.406E+01,1.423E+01,...],
        ...
      ],
      "vinf_arr": [[3.697E+01,3.541E+01,3.375E+01,3.203E+01,3.025E+01,2.847E+01,...],
        [3.340E+01,3.198E+01,3.047E+01,2.890E+01,2.729E+01,2.567E+01,2.408E+01,...],
        [3.033E+01,2.902E+01,2.764E+01,2.620E+01,2.473E+01,2.325E+01,2.180E+01,...],
        [2.767E+01,2.645E+01,2.518E+01,2.385E+01,2.250E+01,2.114E+01,1.981E+01,...],
        ...
      ],  
      "phase_ang": [[6.4298E+00,5.8526E+00,5.2606E+00,4.6357E+00,4.0510E+00,...],
        [7.4765E+00,6.7788E+00,6.1325E+00,5.5678E+00,5.2216E+00,5.4052E+00,...],
        [8.7985E+00,8.0183E+00,7.3591E+00,6.8813E+00,6.7419E+00,7.2113E+00,...],
        [1.0339E+01,9.4884E+00,8.8283E+00,8.4330E+00,8.4597E+00,9.1452E+00,...],
        ...
      ],
      "earth_dist": [[1.6063E+00,1.6045E+00,1.6300E+00,1.6808E+00,1.7533E+00,...],
        [1.6045E+00,1.6300E+00,1.6808E+00,1.7533E+00,1.8434E+00,1.9472E+00,...],
        [1.6300E+00,1.6808E+00,1.7533E+00,1.8434E+00,1.9472E+00,2.0605E+00,...],
        [1.6808E+00,1.7533E+00,1.8434E+00,1.9472E+00,2.0605E+00,2.1801E+00,...],
        ...
      ],  
      "elong_arr":[[1.6574E+02,1.6483E+02,1.5656E+02,1.4637E+02,1.3609E+02,...],
        [1.6483E+02,1.5656E+02,1.4637E+02,1.3609E+02,1.2625E+02,1.1700E+02,...],
        [1.5656E+02,1.4637E+02,1.3609E+02,1.2625E+02,1.1700E+02,1.0838E+02,...],
        [1.4637E+02,1.3609E+02,1.2625E+02,1.1700E+02,1.0838E+02,1.0032E+02,...],
        ...
      ],
      "decl_dep": [[2.9591E+01,2.9972E+01,3.1243E+01,3.3468E+01,3.6492E+01,...],
        [3.1698E+01,3.2147E+01,3.3593E+01,3.6064E+01,3.9305E+01,4.2706E+01,...],
        [3.4107E+01,3.4614E+01,3.6226E+01,3.8918E+01,4.2302E+01,4.5564E+01,...],
        [3.6815E+01,3.7361E+01,3.9123E+01,4.1994E+01,4.5426E+01,4.8384E+01,...],
        ...
      ],
      "approach_ang": [[9.3004E+01,9.2422E+01,9.2084E+01,9.2059E+01,9.2424E+01,...],
        [9.4360E+01,9.3829E+01,9.3555E+01,9.3608E+01,9.4066E+01,9.5026E+01,...],
        [9.5742E+01,9.5257E+01,9.5047E+01,9.5181E+01,9.5737E+01,9.6817E+01,...],
        [9.7161E+01,9.6718E+01,9.6571E+01,9.6788E+01,9.7449E+01,9.8657E+01,...],
        ...
      ]
    }
    

> NOTE: the arrays presented in this example JSON file have been reduced for convenience.

### Mode `T`

In mode `T`, the JSON payload contains the list of objects that come closest to the user-specified orbit.

* `md_constraints` \- summarizes the user-specified constraints (orbit parameters, time span, maximum distance, and maximum number of records)
    * `ec` \- eccentricity of the reference orbit.
    * `qr` \- perihelion distance of the reference orbit.
    * `tp` \- time of perihelion passage of the reference orbit (JD).
    * `in` \- inclination of the reference orbit (deg).
    * `om` \- longitude of the ascending node of the reference orbit (deg).
    * `w` \- argument of periapsis of the reference orbit (deg).
    * `maxdist` \- (if `maxdist` was provided) maximum admissible close-approach distance (au).
    * `maxout` \- maximum number of output records.
    * `jd0` \- start of the time span (JD).
    * `jdf` \- end of the time span (JD). Time span must not be longer than one year.
* `count` \- number of objects in the output list.
* `fields` \- list of fields in the output table:
    * `full_name` \- object full name.
    * `date` \- date of closest approach (calendar).
    * `jd` \- date of closest approach (JD).
    * `min_dist_au` \- close-approach distance (au).
    * `min_dist_km` \- close-approach distance (km).
    * `rel_vel` \- relative velocity at closest approach (km/s).
    * `class` \- small-body orbit class.
    * `H` \- absolute magnitude.
    * `condition_code` \- orbit condition code.
    * `neo` \- near-Earth object flag.
    * `pha` \- potentially hazardous asteroid flag.
    * `sats` \- number of satellites.
    * `spkid` \- SPK object id.
    * `pdes` \- primary designation.
* `data` \- output data arranged in an array where each entry corresponds to a small body and the fields are defined by `fields`.

In mode `T`, the JSON payload will be similar to:

    {
      "signature" : {"source" : "NASA/JPL Small-Body Mission Design API", "version" : "1.1"},
      "md_constraints" : {
        "ec" : "0.2056408220896557",
        "in" : "7.003733902930839",
        "jd0" : "2458849.5",
        "jdf" : "2459132.5",
        "maxdist" : "0.10",
        "maxout" : "100",
        "om" : "48.30597718083336",
        "qr" : "0.3074958016246215",
        "tp" : "2459067.6508400026",
        "w" : "29.18348714438387"
      },
      "count" : "87",
      "fields" : ["full_name","date","jd","min_dist_au","min_dist_km","rel_vel","class","H","condition_code","neo","pha","sats","spkid","pdes"],
      "data" : [
          ["       (2019 XQ)","2020-04-10",58949,"4.404063349100577E-03",658838.499453357,"1.165877291970840E-02","ATE","23.8","7","Y","N","0","3893862","2019 XQ"],
          ["       (2017 WR)","2020-06-02",59002,"8.116836162667585E-03",1214261.40675583,"9.998399964863399E-03","ATE","24.8","6","Y","N","0","3789455","2017 WR"
          ...
        ]
    }
    

HTTP Response Codes
-------------------

All errors are returned via appropriate HTTP response codes. Note that it is possible to submit query parameters resulting in no matching data. In such cases, a non-error code of 200 is returned so the user is responsible for checking the payload if they wish to detect a null-result.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal successful result: data returned (may be empty) or an error message (see [API internal exceptions](#exceptions)) |
| 300 | Multiple Choices | the specified parameters matched more than one object: matching list provided (see [Multiple Objects Matching `sstr`](#multiple_matches)) |
| 400 | Bad Request | the request contained invalid keywords and/or content (details returned in the JSON payload) |
| 405 | Method Not Allowed | the request used an incorrect method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | the database is not available at the time of the request |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

### API Internal Exceptions

Normal returns with HTTP code 200 might include error messages or warnings related to special exceptions:

* `{ "message" : "specified object was not found" }` \- the `des`, `spk`, or `sstr` provided was not found in the Small Body Database. No data will be provided.
* `{ "warning" : "pre-computed missions are based on an outdated orbit solution" }` \- the mission options stored in the Small Body Database were computed with an orbit solution that is no longer the latest, i.e. `orbit_id` does not match `md_orbit_id`. All data will still be provided.
* `{ "warning" : "no pre-computed missions for specified object" }` \- no feasible missions were found under the default constraints. At least the `signature` and `object` (mode `Q` only) objects will be provided. If the orbit is closed, `dv_lowthrust` will also be provided.
* `{ "warning" : "orbit solution is highly uncertain (condition_code >= 7)" }` \- based on its condition code, the current orbit solution is highly uncertain. All data will still be provided.
* `{ "warning" : "no missions found for the current set of constraints" }` \- no accessible targets were found in the SBDB using the current set of constraints.

### Multiple Objects Matching `sstr`

For normal cases where the query matches a single object, the complete data set is returned in JSON-format. In cases where more than one object matches, a list is returned with a primary designation `pdes` and corresponding full-name for each matching object. From `pdes`, it should be possible to submit a new query with `des` set to the `pdes` of interest. Here’s an example result from the query `sstr=141P`.

    {
      "signature":{"version":"1.1","source":"NASA/JPL Small-Body Mission Design API"},
      "code": 300,
      "message": "specified query matched more than one object",
      "list": [
        { "pdes": "141P", "name": "141P/Machholz 2" },
        { "pdes": "141P-A", "name": "141P/Machholz 2-A" },
        { "pdes": "141P-D", "name": "141P/Machholz 2-D" }
      ]
    }
    

From these results, it should be possible to provide a list of links to the appropriate URL with the corresponding `pdes` set in `des` for each possible match. Assuming the user wanted 141P (parent comet, no fragment letter), they would use `des=141P` which would match only that designation (unlike using `sstr=141P` which will match the parent and both fragments).

Change Log
----------

### Version 1.1 (2021 March)

* Added Modes `A`, `T` and low-thrust gravity-assist data.

### Version 1.0 (2018 March)

* Initial release.