Asteroids - NeoWs
-----------------

NeoWs (Near Earth Object Web Service) is a RESTful web service for near earth Asteroid information. With NeoWs a user can: search for Asteroids based on their closest approach date to Earth, lookup a specific Asteroid with its NASA JPL small body id, as well as browse the overall data-set.Data-set: All the data is from the NASA JPL Asteroid team ([http://neo.jpl.nasa.gov/).This](http://neo.jpl.nasa.gov/).This) API is maintained by [SpaceRocks Team: David Greenfield, Arezu Sarvestani, Jason English and Peter Baunach](https://github.com/SpaceRocks/).

Neo - Feed
----------

Retrieve a list of Asteroids based on their closest approach date to Earth. `GET` [`https://api.nasa.gov/neo/rest/v1/feed?start_date=START_DATE&end_date=END_DATE&api_key=API_KEY`](https://api.nasa.gov/neo/rest/v1/feed?start_date=START_DATE&end_date=END_DATE&api_key=API_KEY)

### Query Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| start_date | YYYY-MM-DD | none | Starting date for asteroid search |
| end_date | YYYY-MM-DD | 7 days after start_date | Ending date for asteroid search |
| api_key | string | DEMO_KEY | api.nasa.gov key for expanded usage |

### Example query

 [`https://api.nasa.gov/neo/rest/v1/feed?start_date=2015-09-07&end_date=2015-09-08&api_key=DEMO_KEY`](https://api.nasa.gov/neo/rest/v1/feed?start_date=2015-09-07&end_date=2015-09-08&api_key=DEMO_KEY) 

Neo - Lookup
------------

Lookup a specific Asteroid based on its [NASA JPL small body (SPK-ID) ID](http://ssd.jpl.nasa.gov/sbdb_query.cgi)  `GET` [`https://api.nasa.gov/neo/rest/v1/neo/`](https://api.nasa.gov/neo/rest/v1/neo/)

### Path Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| asteroid_id | int | none | Asteroid SPK-ID correlates to the NASA JPL small body |
| api_key | string | DEMO_KEY | api.nasa.gov key for expanded usage |

### Example query

 [](https://api.nasa.gov/neo/rest/v1/neo/3542519?api_key=DEMO_KEY) [`https://api.nasa.gov/neo/rest/v1/neo/3542519?api_key=DEMO_KEY`](https://api.nasa.gov/neo/rest/v1/neo/3542519?api_key=DEMO_KEY)

Neo - Browse
------------

Browse the overall Asteroid data-set `GET` [`https://api.nasa.gov/neo/rest/v1/neo/browse/`](https://api.nasa.gov/neo/rest/v1/neo/browse/)

### Example query

 [](https://api.nasa.gov/neo/rest/v1/neo/browse?api_key=DEMO_KEY) [`https://api.nasa.gov/neo/rest/v1/neo/browse?api_key=DEMO_KEY`](https://api.nasa.gov/neo/rest/v1/neo/browse?api_key=DEMO_KEY)


[View complete documentation](https://www.postman.com/miguelolave/documentation/3419756-7fff885d-e119-419f-bcd7-7fcaae4c2ab7)