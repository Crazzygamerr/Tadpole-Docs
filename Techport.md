Techport
========

API to make NASA technology project data available in a machine-readable format

Welcome to TechPort - NASA's resource for collecting and sharing information about NASA-funded technology development. Techport allows the public to discover the technologies NASA is working on every day to explore space, understand the universe, and improve aeronautics. NASA is developing technologies in areas such as propulsion, nanotechnology, robotics, and human health. NASA is committed to making its data available and machine-readable through an Application Programming Interface (API) to better serve its user communities. As such, the NASA TechPort system provides a RESTful web services API to make technology project data available in a machine-readable format. This API can be used to export TechPort data into either an XML or a JSON format, which can then be further processed and analyzed. Complete documentation (in Swagger 2.0 format) of the available objects, properties, RESTful URIs is available in the online API specification at: [https://api.nasa.gov/techport/api/specification?api\_key=DEMO\_KEY](https://api.nasa.gov/techport/api/specification?api_key=DEMO_KEY). In general, queries can be issued to the system with the following URI format:

`GET` [`https://api.nasa.gov/techport/api/projects/{id_parameter}?api_key=DEMO_KEY`](https://api.nasa.gov/techport/api/projects/%7Bid_parameter%7D?api_key=DEMO_KEY)

| Parameter | Required? | Value | Description |
| --- | --- | --- | --- |
| id_parameter | Yes | Type: int Default: None | The id value of the TechPort record. ID values can be obtained through the standard TechPort search feature and are visible in the website URLs, e.g. http://techport.nasa.gov/view/0000, where 0000 is the ID value. Alternatively, a request to /api/projects will display all valid IDs in the system. |

### Example Queries

* `GET` [`https://api.nasa.gov/techport/api/projects/17792?api_key=DEMO_KEY`](https://api.nasa.gov/techport/api/projects/17792?api_key=DEMO_KEY)
    
* `GET` [`https://api.nasa.gov/techport/api/projects/17792.xml?api_key=DEMO_KEY`](https://api.nasa.gov/techport/api/projects/17792.json?api_key=DEMO_KEY)
    
* `GET` [`https://api.nasa.gov/techport/api/projects?api_key=DEMO_KEY`](https://api.nasa.gov/techport/api/projects?api_key=DEMO_KEY)
    
* `GET` [`https://api.nasa.gov/techport/api/projects?updatedSince=2016-01-01&api_key=DEMO_KEY`](https://api.nasa.gov/techport/api/projects?updatedSince=2016-01-01&api_key=DEMO_KEY)
    

For more information on Techport and additional example usage please visit [](http://techport.nasa.gov)[http://techport.nasa.gov](http://techport.nasa.gov).