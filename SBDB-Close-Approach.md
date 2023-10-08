SBDB Close-Approach Data API
============================

**Version: 1.5** (2023 March)  
[change log](#change_log)

This API provides access to current close-approach data for all asteroids and comets in JPL’s [SBDB](https://ssd.jpl.nasa.gov/tools/sbdb_lookup.html) (Small-Body DataBase). Defaults for query parameters are setup for a typical CNEOS web-site search: NEO Earth close-approaches less than 0.05 au in the next 60 days sorted by date.

HTTP Request
------------

GET `https://ssd-api.jpl.nasa.gov/cad.api`

### Example Queries

* get all close-approach data for asteroid 433 Eros within 0.2 au between 1900-Jan-01 and 2100-Jan-01:
    * `https://ssd-api.jpl.nasa.gov/cad.api?des=433&date-min=1900-01-01&date-max=2100-01-01&dist-max=0.2`
* get Earth close-approach data for NEOs within 10 lunar distances on or after 2018-Jan-01 sorted by distance
    * `https://ssd-api.jpl.nasa.gov/cad.api?dist-max=10LD&date-min=2018-01-01&sort=dist`

Query Parameters
----------------

Most query parameters are filters effectively limiting the data to those matching the constraints, a few are object selectors (limit data to those matching the specified object), and one is a sort key. Filter-type query parameters are “additive” in that they are combined with logical AND when applied to the data. Boolean-type filter parameters are only applied when true. For example, setting “neo=false” simply disables that filter (it does not select non-NEOs).

| Parameter | Type | Default | Function | Description |
| --- | --- | --- | --- | --- |
| date-min | string | “now” | filter | exclude data _earlier than_ this date `YYYY-MM-DD` or date/time `YYYY-MM-DDThh:mm:ss` or `now` for the current date |
| date-max | string | “+60” | filter | exclude data _later than_ this date `YYYY-MM-DD` or date/time `YYYY-MM-DDThh:mm:ss` or `now` for the current date or `+D` for “D” days after now (“D” must not be greater than 36525; be sure to URL-encode the plus sign `+` as `%2B`) |
| dist-min | string | none | filter | exclude data with an approach distance less than this, e.g., `0.05`, `10LD` (default units: au) |
| dist-max | string | “0.05” | filter | exclude data with an approach distance greater than this (see dist-min) |
| min-dist-min | string | none | filter | exclude data with an approach minimum-distance less than this, e.g., `0.05`, `10LD` (default units: au) |
| min-dist-max | string | none | filter | exclude data with an approach minimum-distance greater than this (see min-dist-min) |
| h-min | number | none | filter | exclude data from objects with H-values _less_ than this (e.g., `22` meaning objects _smaller_ than this) |
| h-max | number | none | filter | exclude data from objects with H-value _greater_ than this (e.g., `17.75` meaning _objects_ larger than this) |
| v-inf-min | number | none | filter | exclude data with V-infinity _less_ than this positive value in km/s (e.g., `18.5`) |
| v-inf-max | number | none | filter | exclude data with V-infinity _greater_ than this positive value in km/s (e.g., `20`) |
| v-rel-min | number | none | filter | exclude data with V-relative _less_ than this positive value in km/s (e.g., `11.2`) |
| v-rel-max | number | none | filter | exclude data with V-relative _greater_ than this positive value in km/s (e.g., `19`) |
| class | string | none | filter | limit data to objects with the specified orbit-class (e.g., `ATE`; see [list of valid class values below](#sbdb_class_table)) |
| pha | boolean | false | filter | limit data to PHAs |
| nea | boolean | false | filter | limit data to NEAs |
| comet | boolean | false | filter | limit data to comets |
| nea-comet | boolean | false | filter | limit data to NEAs and comets |
| neo | boolean | true | filter | limit data to NEOs |
| kind | string | none | filter | limit data to objects of the specified kind (`a`=asteriod, `an`=numbered-asteroids, `au`=unnumbered-asteroids, `c`=comets, `cn`=numbered-comets, `cu`=unnumbered-comets, `n`=numbered-objects, and `u`=unnumbered-objects) |
| spk | int | none | selector | only data for the object matching this SPK-ID (e.g., `2000433` ) |
| des | string | none | selector | only data for the object matching this designation (e.g., `2015 AB` or `141P` or `433`) \[NOTE: when submitting a des containing a space in your query string, you must replace the space with `%20`, for example `2015%20AB`\] |
| body | string | “Earth” | selector | limit data to close-approaches to the specified body (e.g., `Earth`) or allow all bodies with `ALL` or `*` (see [Close Approach Bodies](#cad_body_table) section below) |
| sort | string | “date” | sorter | sort data on the specified field: “date”, “dist”, “dist-min”, “v-inf”, “v-rel”, “h”, or “object” (default sort order is ascending: prepend “-“ for descending) |
| limit | number | none | filter | limit data to the first N results (where N is the specified number and must be an integer value greater than zero); also output `total` available matching records |
| limit-from | number | none | filter | limit data to `limit` results starting from result record N (where N must be an integer greater than zero); `limit` must be specified |
| total-only | boolean | false | output | output the `total` number of records matching the specified filter ignoring `limit` and `limit-from`; do not output any data |
| diameter | boolean | false | output | include known `diameter` and `diameter_sigma` values |
| fullname | boolean | false | output | include the full-format object name/designation |

Data Output
-----------

> Please **always check** the JSON payload “signature” object for the API “version”. If the version does not match the version in this document (at the top), there is no guarantee that the format has not changed.
> 
> Example "signature" object with "version" value "1.0": `"signature":{"version":"1.0","source":"NASA/JPL ... API"}`

Successful query requests result in a JSON-format data payload. The specific content depends on the query mode. If a search is too restrictive, it is possible for a zero-count result (see below).

Each CAD record is packaged as an array of fields (corresponding to those listed) in the following order:

* **des** \- primary designation of the asteroid or comet (e.g., `443`, `2000 SG344`)
* **orbit_id** \- orbit ID used for the close-approach computation
* **jd** \- time of close-approach (JD Ephemeris Time, TDB)
* **cd** \- time of close-approach (formatted calendar date/time, TDB)
* **dist** \- nominal approach distance (au)
* **dist_min** \- minimum (3-sigma) approach distance (au)
* **dist_max** \- maximum (3-sigma) approach distance (au)
* **v_rel** \- velocity relative to the approach body at close approach (km/s)
* **v_inf** \- velocity relative to a massless body (km/s)
* **t\_sigma\_f** \- 3-sigma uncertainty in the time of close-approach (formatted in days, hours, and minutes; days are not included if zero; example “`13:02`” is 13 hours 2 minutes; example “`2_09:08`” is 2 days 9 hours 8 minutes)
* **body** \- name of the close-approach body (e.g., `Earth`)
    * only output if the body query parameters is set to `ALL`
* **h** \- absolute magnitude H (mag)
* **diameter** \- diameter of the body (km)
    * optional - only output if requested with the `diameter` query parameter
    * `null` if not known
* **diameter_sigma** \- 1-sigma uncertainty in the diameter of the body (km)
    * optional - only output if requested with the `diameter` query parameter
    * `null` if not known
* **fullname** \- formatted full-name/designation of the asteroid or comet
    * optional - only output if requested with the `fullname` query parameter
    * formatted with leading spaces for column alignment in monospaced font tables

### Sample Data Output

Here is an example of the JSON-format output for a query resulting in 3 records for close-approaches to the Earth with a minimum distance of 1LD.

    {
      "signature":{"source" : "NASA/JPL SBDB Close Approach Data API","version" : "1.5"},
      "count":3,
      "fields":["des","orbit_id","jd","cd","dist","dist_min","dist_max","v_rel","v_inf","t_sigma_f","h","diameter","diameter_sigma","fullname"],
      "data":[
        ["153814","174","2461948.724524223","2028-Jun-26 05:23","0.00166253924938707","0.00166237672775144","0.00166270177137481","10.2426019613426","10.084918538826","< 00:01","18.33","0.932","0.011","153814 (2001 WN5)"],
        ["99942","206","2462240.407091595","2029-Apr-13 21:46","0.000254099098170977","0.000254085852623379","0.000254112343772133","7.42249308586014","5.84135545611464","< 00:01","19.7","0.34","0.04"," 99942 Apophis (2004 MN4)"],
        ["2001 AV43","42","2462452.142037054","2029-Nov-11 15:25","0.00209271674918052","0.00209125158265035","0.00209418316351851","3.99789389003422","3.66561381185116","00:03","24.6",null,null,"       (2001 AV43)"]
       ]
    }
    

Here’s an example of a zero-count result (e.g., a query is too restrictive).

    {
      "signature":{"version":"1.5","source":"NASA/JPL SBDB Close Approach Data API"},
      "count" : 0
    }
    

### SBDB Orbit Class Values

| Class | Description |
| --- | --- |
| IEO | Atira An asteroid orbit contained entirely within the orbit of the Earth (Q < 0.983 AU). Also known as an Interior Earth Object. |
| ATE | Aten Near-Earth asteroid orbits similar to that of 2062 Aten (a &lt; 1.0 AU; Q &gt; 0.983 AU). |
| APO | Apollo Near-Earth asteroid orbits which cross the Earth’s orbit similar to that of 1862 Apollo (a > 1.0 AU; q < 1.017 AU). |
| AMO | Amor Near-Earth asteroid orbits similar to that of 1221 Amor (1.017 AU < q < 1.3 AU). |
| MCA | Mars-crossing Asteroid Asteroids that cross the orbit of Mars constrained by (1.3 AU < q < 1.666 AU; a < 3.2 AU). |
| IMB | Inner Main-belt Asteroid Asteroids with orbital elements constrained by (a &lt; 2.0 AU; q &gt; 1.666 AU). |
| MBA | Main-belt Asteroid Asteroids with orbital elements constrained by (2.0 AU &lt; a < 3.2 AU; q &gt; 1.666 AU). |
| OMB | Outer Main-belt Asteroid Asteroids with orbital elements constrained by (3.2 AU < a < 4.6 AU). |
| TJN | Jupiter Trojan Asteroids trapped in Jupiter’s L4/L5 Lagrange points (4.6 AU < a < 5.5 AU; e < 0.3). |
| CEN | Centaur Objects with orbits between Jupiter and Neptune (5.5 AU < a < 30.1 AU). |
| TNO | TransNeptunian Object Objects with orbits outside Neptune (a > 30.1 AU). |
| PAA | Parabolic Asteroid Asteroids on parabolic orbits (e = 1.0). |
| HYA | Hyperbolic Asteroid Asteroids on hyperbolic orbits (e > 1.0). |
| HYP | Hyperbolic Comet Comets on hyperbolic orbits (e > 1.0). |
| PAR | Parabolic Comet Comets on parabolic orbits (e = 1.0). |
| COM | Comet Comet orbit not matching any defined orbit class. |
| JFC | Jupiter-family Comet* Jupiter-family comet, classical definition (P < 20 y). |
| HTC | Halley-type Comet* Halley-type comet, classical definition (20 y < P < 200 y). |
| ETc | Encke-type Comet Encke-type comet, as defined by Levison and Duncan (Tj > 3; a < aJ). |
| CTc | Chiron-type Comet Chiron-type comet, as defined by Levison and Duncan (Tj > 3; a > aJ). |
| JFc | Jupiter-family Comet Jupiter-family comet, as defined by Levison and Duncan (2 < Tj < 3). |

### Close Approach Bodies

The following bodies may be selected via the `body` query parameter. For example, use `body=Juptr` to select close approaches to planet Jupiter.

| Value | Body |
| --- | --- |
| `Merc` | Mercury |
| `Venus` | Venus |
| `Earth` | Earth |
| `Mars` | Mars |
| `Juptr` | Jupiter |
| `Satrn` | Saturn |
| `Urnus` | Uranus |
| `Neptn` | Neptune |
| `Pluto` | Pluto |
| `Moon` | Moon |

HTTP Response Codes
-------------------

All errors are returned via appropriate HTTP response codes. Note that it is possible to submit query parameters resulting in no matching data. In such cases, a non-error code of 200 is returned so the user is responsible for checking the payload if they wish to detect a null-result.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal successful result: array of CA data returned (may be empty) |
| 400 | Bad Request | the request contained invalid keywords and/or content (details returned in the JSON payload) |
| 405 | Method Not Allowed | the request used an incorrect method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | the database is not available at the time of the request |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

* * *

Change Log
----------

### Version 1.5 (2023 March)

* Added additional filter query parameters `limit-from` and `total-only`.
* Fields `count` and `total` now output as JSON numbers instead of strings.
* Improved back-end data retrieval efficiency.

### Version 1.4 (2021 July)

* Added additional filter query parameters `min-dist-min` and `min-dist-max`.

### Version 1.3 (2021 March)

* Added boolean query parameter `diameter` to make the output of the new `diameter` and `diameter_sigma` values optional.

### Version 1.2 (2021 March)

* Added output of `diameter` and `diameter_sigma`. These values will be `null` when not available.

### Version 1.1 (2016 September)

* Updated default values for many query parameters such that the default query (i.e., with no query parameters set) results in data of typical interest (as opposed to dumping the entire data set).

### Version 1.0 (2016 August)

* Initial release