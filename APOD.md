APOD: Astronomy Picture of the Day
====

One of the most popular websites at NASA is the [Astronomy Picture of the Day](http://apod.nasa.gov/apod/astropix.html).

In fact, this website is one of the [most popular websites](https://analytics.usa.gov) across all federal agencies. It has the popular appeal of a Justin Bieber video. This endpoint structures the APOD imagery and associated metadata so that it can be repurposed for other applications. In addition, if the `concept_tags` parameter is set to `True`, then keywords derived from the image explanation are returned. These keywords could be used as auto-generated hashtags for twitter or instagram feeds; but generally help with discoverability of relevant imagery. The full documentation for this API can be found in the [APOD API Github repository](https://github.com/nasa/apod-api).

### HTTP Request

`GET https://api.nasa.gov/planetary/apod`

**concept_tags are now disabled in this service. Also, an optional return parameter** _**copyright**_ **is returned if the image is not public domain.**

### Query Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| date | YYYY-MM-DD | \_today\_ | The date of the APOD image to retrieve |
| start_date | YYYY-MM-DD | none | The start of a date range, when requesting date for a range of dates. Cannot be used with \`date\`. |
| end_date | YYYY-MM-DD | \_today\_ | The end of the date range, when used with \`start_date\`. |
| count | int | none | If this is specified then \`count\` randomly chosen images will be returned. Cannot be used with \`date\` or \`start\_date\` and \`end\_date\`. |
| thumbs | bool | False | Return the URL of video thumbnail. If an APOD is not a video, this parameter is ignored. |
| api_key | string | DEMO_KEY | api.nasa.gov key for expanded usage |

### Example query [`https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY`](https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY)

﻿  

[View complete documentation](https://www.postman.com/miguelolave/documentation/3419756-125f35e6-d0cb-4c30-885d-c239a5c8b3d8)