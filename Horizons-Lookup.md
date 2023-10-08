Horizons Lookup API
===================

**Version: 1.0** (2021 September)  
[change log](#change_log)

Process a user-specified name, designation, SPK-ID, IAU number, MPC packed designation, or other historical alias, and return in a standardized format its primary synonyms and all aliases recognized by JPL’s [Horizons system](https://ssd.jpl.nasa.gov/horizons/) as being linked to publicly available trajectory data.

This API is intended for telescope schedulers and others who need to correlate one of the many object labels typically possible with Horizons output and other sources.

For example, one might request an ephemeris for asteroid “1954 SO1” from Horizons. This API can be used to determine the object is also known as IAU-numbered object “17 Thetis”, has historical alternate designations of “A916 YF”, “A913 CA”, an MPC 7-character packed designation of “I52H00A”, and that an SPK file request will come back from Horizons with the name “2000017.bsp”.

HTTP Request
------------

GET `https://ssd.jpl.nasa.gov/api/horizons_lookup.api`

### Example Queries

* Retrieve (In JSON format) all aliases for objects having the designation “2004 MN4”
    * `https://ssd.jpl.nasa.gov/api/horizons_lookup.api?sstr=2004%20MN4`
* Retrieve (in text format) all aliases for objects containing the string “Halley”:
    * `https://ssd.jpl.nasa.gov/api/horizons_lookup.api?sstr=Halley&format=text`

Query Parameters
----------------

| Parameter | Type | Default | Allowed values | Description |
| --- | --- | --- | --- | --- |
| sstr | string | _none_ | _discussion below_ | Search string containing object name, designation, SPK-ID, IAU number, or MPC packed-format designation |
| group | string | _none_ | `ast`,`com`,`pln`,`sat` | Optional object group limiter: use `ast` to limit search to asteroid only, `com` for comets only, `pln` for planets _or_ spacecraft only, and `sat` for planetary satellites only |
| format | string | `json` | `json`,`text` | Specify output format: `json` for JSON or `text` for plain-text (use `text` for backward compatibility with prior obsolete CGI implementation) |

Usage and examples
------------------

The `sstr` parameter can refer to a planet, asteroid, comet, natural satellite, or spacecraft.

| Type of specification | Example |
| --- | --- |
| name | `sstr=Juno` |
| designation | `sstr=1990 MU` |
| SPK ID | `sstr=2101955` |
| IAU number | `sstr=1` |
| MPC packed designation | `sstr=J89A00C` |

The search is **case insensitive**, but **space sensitive**.

If the type of object is known, the optional `group` parameter provides four limiters to restrict search results to a single type of object:

* `ast` (asteroids only)
* `com` (comets only)
* `pln` (planets OR spacecraft only)
* `sat` (natural satellites only)

For example, `sstr=Juno` would currently return data for three objects: the asteroid “3 Juno” as well as the Juno spacecraft and the Juno Centaur booster.

If it is known a-priori that only an asteroid was of interest, `sstr=Juno&group=ast` would return data only for asteroids and exclude spacecraft matches. Conversely, `sstr=Juno&group=pln` would return only spacecraft trajectories, not asteroids.

Similarly, `sstr=Halley` would return data for the comet AND asteroid (and perhaps any future spacecraft trajectories using that name), whereas `sstr=Halley&group=com` would return data only for comets. `sstr=1P`, the comet designation, would also work to limit results to comets.

### Allowable Characters for `sstr`

The `sstr` parameter may contain any combination of alphanumeric characters, space, `/`, `'`, `-`, and `.`.

If the `sstr` parameter contains any space or other URI-reserved characters, it will be necessary to URL-encode such characters. For example, to search for “1990 MU”, use `sstr=1990%20MU`.

Data Output
-----------

> Please **always check** the JSON payload “signature” object for the API “version”. If the version does not match the version in this document (at the top), there is no guarantee that the format has not changed.
> 
> Example "signature" object with "version" value "1.0": `"signature":{"version":"1.0","source":"NASA/JPL ... API"}`

A successful query returns results in JSON-format (by default) as shown in the example below. A query that does no match any known object is considered successful but will return a result with the JSON “count” keyword set to zero (“0”).

JSON-format results are provided in the JSON “result” array which contains one or more JSON-object for each matching Horizons body.

| JSON keyword | text-format equivalent | Description |
| --- | --- | --- |
| “name” | “Object name” | Name of the matching object (for numbered asteroids, this will contain the IAU-number as well as the IAU-name, e.g., `3 Juno`) |
| “pdes” | “Primary designation” | Primary provisional designation (`null` if not known) |
| “spkid” | “Primary SPKID” | Primary SPK ID |
| “alias” | “Aliases” | A list of alternate designations, SPK IDs, etc. (`[]` if none) |

For plain-text format results, see examples below under the heading [Sample `text`-format Output](#text).

### Sample `json`-format Output

Here is an example of output for a singular match:

`https://ssd.jpl.nasa.gov/api/horizons_lookup.api?sstr=2004%20MN4`

    {
      "signature" : {
        "source" : "NASA/JPL Horizons Lookup API",
        "version" : "1.0"
      },
      "count" : 1,
      "result" : [
        {
          "alias" : [ "3264226", "K04M04N" ],
          "name" : "99942 Apophis",
          "spkid" : "2099942",
          "pdes" : "2004 MN4"
        }
      ]
    }
    

Here is an example of output for multiple matches:

`https://ssd.jpl.nasa.gov/api/horizons_lookup.api?sstr=Juno`

    {
      "signature" : {
        "source" : "NASA/JPL Horizons Lookup API",
        "version" : "1.0"
      },
      "count" : 3,
      "result" : [
        {
          "name" : "Juno (spacecraft)",
          "pdes" : null,
          "spkid" : "-61",
          "alias" : []
        },
        {
          "name" : "Juno Centaur Stage (spacecraft)",
          "pdes" : null,
          "spkid" : "-610",
          "alias" : []
        },
        {
          "name" : "3 Juno",
          "pdes" : "A804 RA",
          "spkid" : "2000003",
          "alias" : [ "I04R00A" ]
        }
      ]
    }
    

Here is an example of output for no matching objects:

`https://ssd.jpl.nasa.gov/api/horizons_lookup.api?sstr=ZZZZ`

    {
      "signature" : {
        "source" : "NASA/JPL Horizons Lookup API",
        "version" : "1.0"
      },
      "count" : "0"
    }
    

### Sample `text`-format Output

> Note that records are delimited by a trailing blank line.

Here is an example of output for a singular match:

`https://ssd.jpl.nasa.gov/api/horizons_lookup.api?sstr=2004%20MN4&format=text`

    API VERSION: 1.0
    API SOURCE: NASA/JPL Horizons Lookup API
    
    Object name        = 99942 Apophis
    Primary SPKID      = 2099942
    Primary designation= 2004 MN4
    Aliases            = 3264226, K04M04N
    
    

Here is an example of output for multiple matches:

`https://ssd.jpl.nasa.gov/api/horizons_lookup.api?sstr=Juno&format=text`

    API VERSION: 1.0
    API SOURCE: NASA/JPL Horizons Lookup API
    
    Object name        = Juno (spacecraft)
    Primary SPKID      = -61
    Primary designation=
    Aliases            =
    
    Object name        = Juno Centaur Stage (spacecraft)
    Primary SPKID      = -610
    Primary designation=
    Aliases            =
    
    Object name        = 3 Juno
    Primary SPKID      = 2000003
    Primary designation= A804 RA
    Aliases            = I04R00A
    
    

Here is an example of output for no matching objects:

`https://ssd.jpl.nasa.gov/api/horizons_lookup.api?sstr=ZZZZ&format=text`

    API VERSION: 1.0
    API SOURCE: NASA/JPL Horizons Lookup API
    
    NOTICE: no matches found
    

HTTP Response Codes
-------------------

Successful matches return one or more entry listings. Error messages and equivalent integer codes are returned otherwise, as described below.

It is possible to submit a search that produces no matching data. In such cases, a 200 error code is returned but the JSON “count” keyword has a value of “0”.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal result |
| 400 | Bad Request | request contained invalid keywords and/or content |
| 405 | Method Not Allowed | the request used an incorrect HTTP method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | necessary databases are not available (server-side error) |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

* * *

Change Log
----------

### Version 1.0 (2021 September)

* Initial release