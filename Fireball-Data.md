Fireball Data API
=================

**Version: 1.0** (2016 August)  
[change log](#change_log)

The fireball data API provides a method of requesting specific records from the available data-set. Every successful query will return content representing one or more fireball data records. See the CNEOS page on [fireballs](https://cneos.jpl.nasa.gov/fireballs/) for details on this data-set.

HTTP Request
------------

GET `https://ssd-api.jpl.nasa.gov/fireball.api`

### Example Queries

* `https://ssd-api.jpl.nasa.gov/fireball.api` (return all available data in reverse-chronologic order)
* `https://ssd-api.jpl.nasa.gov/fireball.api?limit=20` (return the most recent 20 records)
* `https://ssd-api.jpl.nasa.gov/fireball.api?date-min=2014-01-01&req-alt=true` (return data on or after 2014-01-01 and only records with the altitude defined)

Query Parameters
----------------

| Parameter | Type | Default | Function | Description |
| --- | --- | --- | --- | --- |
| date-min | string | none | filter | exclude data _earlier than_ this date `YYYY-MM-DD` or date/time `YYYY-MM-DDThh:mm:ss` |
| date-max | string | none | filter | exclude data _later than_ this date `YYYY-MM-DD` or date/time `YYYY-MM-DDThh:mm:ss` |
| energy-min | string | none | filter | exclude data with total-radiated-energy less than this positive value in joules ×1010 (e.g., `0.3` = 0.3×1010 joules) |
| energy-max | string | none | filter | exclude data with total-radiated-energy greater than this (see energy-min) |
| impact-e-min | string | none | filter | exclude data with estimated impact energy less than this positive value in kilotons (kt) (e.g., `0.08` kt) |
| impact-e-max | string | none | filter | exclude data with total-radiated-energy greater than this (see impact-e-min) |
| vel-min | number | none | filter | exclude data with velocity-at-peak-brightness _less_ than this positive value in km/s (e.g., `18.5`) |
| vel-max | number | none | filter | exclude data with velocity-at-peak-brightness _greater_ than this positive value in km/s (e.g., `20`) |
| alt-min | number | none | filter | exclude data from objects with an altitude _less_ than this (e.g., `22` meaning objects _smaller_ than this) |
| alt-max | number | none | filter | exclude data from objects with an altitude _greater_ than this (e.g., `17.75` meaning _objects_ larger than this) |
| req-loc | boolean | false | filter | location (latitude and longitude) required; when set `true`, exclude data without a location |
| req-alt | boolean | false | filter | altitude required; when set `true`, exclude data without an altitude |
| req-vel | boolean | false | filter | velocity required; when set `true`, exclude data without a velocity |
| req-vel-comp | boolean | false | filter | velocity components required; when set `true`, exclude data without velocity components |
| vel-comp | boolean | false | selector | include velocity components |
| sort | string | “-date” | sorter | sort data on the specified field: “date”, “energy”, “impact-e”, “vel”, or “alt” (default sort order is ascending: prepend “-“ for descending) |
| limit | number | none | filter | limit data to the first N results (where N is the specified number and must be an integer value greater than zero) |

Data Output
-----------

> Please **always check** the JSON payload “signature” object for the API “version”. If the version does not match the version in this document (at the top), there is no guarantee that the format has not changed.
> 
> Example "signature" object with "version" value "1.0": `"signature":{"version":"1.0","source":"NASA/JPL ... API"}`

Data are returned in JSON format as a single object (hash-table). The number of records contained in the object is indicated in the “count” key. In cases where the user-specified constraints are too tight (no matching results), only the “count” and “signature” keys are returned, as in the following example.

    { "count":0, "signature":{"version":"1.0","source":"NASA/JPL Fireball Data API"} }
    

Each record is provided as an element of the “data” object and each record is an array of fields. The names of each field contained in each record is provided in the “fields” object array.

The fields are defined as follows.

| Field | Description |
| --- | --- |
| date | date/time of peak brightness (GMT) |
| lat | latitude at peak brightness (degrees) |
| lon | longitude at peak brightness (degrees) |
| lat-dir | latitude direction (“N” or “S”) |
| lon-dir | latitude direction (“E” or “W”) |
| alt | altitude above the geoid at peak brightness (km) |
| vel | velocity at peak brightness (km/s) |
| energy | approximate total radiated energy (1010 joules) |
| impact-e | approximate total impact energy (kt) |
| vx  | pre-entry estimated velocity (Earth centered X component, km/s) |
| vy  | pre-entry est. velocity (Earth centered Y component, km/s) |
| vz  | pre-entry est. velocity (Earth centered Z component, km/s) |

The following is a sample JSON-format result with `req-alt=true` sorted by `date` and limited (`limit=3`) to 3 records:

    {
      "signature":{"version":"1.0","source":"NASA/JPL Fireball Data API"},
      "count":3,
      "fields":["date","lat","lat-dir","lon","lon-dir","alt","vel","energy","impact-e"],
      "data":[
        ["2015-10-13 12:23:08","8.0","S","52.5","W","38.9",null,"2.3","0.082"],
        ["2015-10-11 00:07:46","55.4","S","18.8","W",null,null,"3.0","0.1"],
        ["2015-10-10 09:57:51","51.0","S","21.1","W","51.8",null,"3.6","0.12"]
      ]
    }
    

### Data Fields

Note that many fields can be undefined (null) in a particular data record. The only fields which are guaranteed to be defined are `date`, `energy`, and `impact-e`. Where the location is known, all four related fields (`lat`, `lat-dir`, `lon`, and `lon-dir`) are defined. Where the location is not known (reported), all four location fields will be null.

The `date` is reported as a string in `YYYY-MM-DD hh:mm:ss` format. Both `lat` and `lon` are reported as strings in decimal degrees. The `lat-dir` field will be either `N` or `S` (or `null`). Similarly, the `lon-dir` field will be either `E` or `W` (or `null`). The `alt` field is reported as string in decimal km and is referenced to the Earth geoid. The `vel` field is reported as a string in decimal km/s. Total radiated energy is reported in the `energy` field as a string in decimal joules × 1010 (for example, a reported value of `3.6` is 3.6 × 1010 joules). Impact energy is reported in the `impact-e` field in units of kilotons (kt).

All fields are relative to the fireball’s peak-brightness event.

HTTP Response Codes
-------------------

All errors are returned via appropriate HTTP response codes.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal successful result: fireball data returned |
| 400 | Bad Request | the request contained invalid keywords and/or content: details returned |
| 405 | Method Not Allowed | the request used an incorrect method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | the database is not available at the time of the request |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

* * *

Change Log
----------

### Version 1.0 (2016 August)

* Initial release