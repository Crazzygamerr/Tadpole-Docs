TLE API
=======

Two line element data for earth-orbiting objects at a given point in time

The TLE API provides up to date two line element set records, the data is updated daily from [CelesTrak](https://celestrak.com) and served in JSON format. A two-line element set (TLE) is a data format encoding a list of orbital elements of an Earth-orbiting object for a given point in time. For more information on TLE data format visit [Definition of Two-line Element Set Coordinate System](https://spaceflight.nasa.gov/realdata/sightings/SSapplications/Post/JavaSSOP/SSOP_Help/tle_def.html).Further documentation and response examples are available at: [](http://tle.ivanstanojevic.me)[http://tle.ivanstanojevic.me](http://tle.ivanstanojevic.me)

### Available endpoints

The TLE API consists of two endpoints

`GET` [`http://tle.ivanstanojevic.me`](http://tle.ivanstanojevic.me)

| Endpoint | Description |
| --- | --- |
| GET /api/tle?search={q} | Performing a search by satellite name |
| GET /api/tle/{q} | Retrieving a single TLE record where query is satellite number |

### Example query

 [`http://tle.ivanstanojevic.me/api/tle`](http://tle.ivanstanojevic.me/api/tle)