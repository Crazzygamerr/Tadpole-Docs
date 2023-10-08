JD Date/Time Converter API
==========================

**Version: 0.1** (2021 February)  
[change log](#change_log)

Given a Julian Day number (JD), return the corresponding calendar date/time. Given a calendar date/time, return the corresponding Julian day number.

HTTP Request
------------

GET `https://ssd-api.jpl.nasa.gov/jd_cal.api`

### Example Queries

* get the calendar date/time corresponding to JD `2451544`
    * `https://ssd-api.jpl.nasa.gov/jd_cal.api?jd=2451544` (default format)
    * `https://ssd-api.jpl.nasa.gov/jd_cal.api?jd=2451544&format=d.5` (decimal days)
    * `https://ssd-api.jpl.nasa.gov/jd_cal.api?jd=2451544&format=s` (formatted to the nearest second)
* get the Julian Day number corresponding to the calendar date/time `2000-01-01 12:00`
    * `https://ssd-api.jpl.nasa.gov/jd_cal.api?cd=2000-01-01_12:00`
    * `https://ssd-api.jpl.nasa.gov/jd_cal.api?cd=2000-01-01%2012:00`

Query Parameters
----------------

| Parameter | Type | Default | Allowable Values | Description |
| --- | --- | --- | --- | --- |
| jd  | number | none | \[`-1931076.5`:`38245308.5`\] | Julian date to convert (UT) |
| cd  | string | none | _see [below](#cd)_ | calendar date/time to convert |
| format | string | `jd.7` | _see [below](#format)_ | format the resulting JD or date/time |

### Calendar Date/Time Allowable Values

The specified calendar date/time (cd) may specify the date only or both a date and time. The date must be specified in year, month, day order as in `YYYY-MM-DD`. The month may be specified using the 3-character (case-insensitive) month abbreviations as defined in the USA (`Jan`, `Feb`, `Mar`, `Apr`, `May`, `Jun`, `Jul`, `Aug`, `Sep`, `Oct`, `Nov`, `Dec`).

Time may be specified as decimal days (e.g., `YYYY-MM-DD.DDDDDD`), hour and minutes (e.g., `YYYY-MM-DD hh:mm`), or hours, minutes and seconds (e.g., `YYYY-MM-DD hh:mm:ss`). When specifying the time using hours and minutes, decimal minutes may be used (e.g., `YYYY-MM-DD hh:mm.m`). Similarly for hours, minutes, and seconds, decimal seconds may be used (e.g., `YYYY-MM-DD hh:mm:ss.s`).

When specifying time, the separator between the specified date and time may be either a space (`%20` when escaped for the URL) or an underbar `_` (e.g., `YYYY-MM-DD_hh:mm`). The latter does not require URL escaping.

### Output Formatting

The output formatted can be controlled to provide one of the following result formats. Each format is described below.

| Format Value | Description |
| --- | --- |
| `d` | nearest integer day |
| `m` | nearest integer minute |
| `s` | nearest integer second |
| `d.#` | decimal days where `#` specifies the number of decimal digits (`1` to `7`) |
| `m.#` | decimal minutes where `#` specifies the number of decimal digits (`1` to `5`) |
| `s.#` | decimal seconds where `#` specifies the number of decimal digits (`1` to `3`) |
| `jd.#` | `#` specifies the number of decimal digits (`1` to `7`) output for Julian days |

#### Calendar Date/Time

The date portion of will always be of the form `YYYY-MM-DD` where `YYYY` is the year (possibly negative), `MM` is the 2-digit numerical month number (e.g., `02`), and `DD` is the 2-digit day-of-the-month number.

The time or fractional days portion can be formatted as follows.

* `format=d` integer day (round the time to the nearest integer day)
* `format=d.6` fractional day from 1-6 digits (e.g., `2000-01-01.5`)
* `format=m` nearest minute in the form `hh:mm` where `hh` is hours and `mm` is minutes
* `format=s` nearest integer seconds (`hh:mm:ss`) where `ss` seconds
* `format=s.3` fractional seconds from 1-3 digits (e.g. `2020-01-01 12:00:00.000`)

#### Julian Day Number

The number of decimal digits (1 to 7) output for the Julian day number can be specified. For example, with 2 digits specified via `format=jd.2`, output would look like `2451544.50`. The default is `jd.7` (7 decimal digits).

Data Output
-----------

### Converting Calendar Date/Time to JD

When converting from calendar date/time to JD, the field `jd` contains the resulting Julian Day number. For example, converting `cd=2000-01-01.0` results in the following JSON payload.

    {
      "jd" : "2451544.5000000",
      "signature" : {
        "source" : "NASA/JPL JD-Calendar Conversion API",
        "version" : "1.0"
      }
    }
    

### Converting JD to Calendar Date/Time

When converting from JD to calendar date/time, the following fields are returned.

| Field | Description |
| --- | --- |
| cd  | Calendar date/time string formatted as requested |
| year | The year of the resulting `cd` |
| month | Numerical month (e.g., `02`) |
| month_name | 3-character month abbreviation (e.g., `Feb`) |
| doy | Day of the year (e.g., `247`) |
| dow | Day of the week (`1` to `7` with `1`=`Sunday`) |
| dow_name | Name of the day of the week (e.g., ‘Monday’) |
| day\_and\_time | Formatted string for just the day and time (useful for formatting custom results) |

For example, converting JD of 2451545.02135422 to calendar date/time requesting 3 decimal digits on seconds results in the following JSON payload.

    {
      "jd" : "2451545.0213542",
      "cd" : "2000-01-01 12:30:45.005",
      "year" : 2000
      "month_name" : "Jan",
      "doy" : 1,
      "dow" : "7",
      "dow_name" : "Saturday",
      "day_and_time" : "01 12:30:45.005",
      "signature" : {
        "source" : "NASA/JPL JD-Calendar Conversion API",
        "version" : "1.0"
      },
    }
    

Calendars
---------

This tool considers both the Gregorian calendar and the Julian calendar. The Gregorian calendar is in use today having replaced the Julian Calendar on 1582-Oct-15 (Gregorian) with the prior day 1582-Oct-4 being the last observed for the Julian calendar. See [https://en.wikipedia.org/wiki/Gregorian_calendar](https://en.wikipedia.org/wiki/Gregorian_calendar) for more information on the Gregorian calendar.

If you input the calendar date/time of `1582-10-04 00:00` or earlier, the resulting Julian day number will be with respect to the Julian calendar. Any date/time after `1582-10-04 00:00` will result in a Julian day number with respect to the Gregorian proleptic calendar.

The following table shows both Gregorian proleptic and Julian proleptic dates associated with Julian day numbers through the 10-day transtion between the two calendars. Those dates shown in bold are those input/output by this API.

| JD  | Gregorian proleptic | Julian proleptic |
| --- | --- | --- |
| 2299149.5 | 1582-10-04 | **1582-09-24** |
| 2299150.5 | 1582-10-05 | **1582-09-25** |
| 2299151.5 | 1582-10-06 | **1582-09-26** |
| 2299152.5 | 1582-10-07 | **1582-09-27** |
| 2299153.5 | 1582-10-08 | **1582-09-28** |
| 2299154.5 | 1582-10-09 | **1582-09-29** |
| 2299155.5 | 1582-10-10 | **1582-09-30** |
| 2299156.5 | 1582-10-11 | **1582-10-01** |
| 2299157.5 | 1582-10-12 | **1582-10-02** |
| 2299158.5 | 1582-10-13 | **1582-10-03** |
| 2299159.5 | 1582-10-14 | **1582-10-04** |
| 2299160.5 | **1582-10-15** | 1582-10-05 |
| 2299161.5 | **1582-10-16** | 1582-10-06 |

HTTP Response Codes
-------------------

All errors are returned via appropriate HTTP response codes.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal successful result: data returned |
| 400 | Bad Request | the request contained invalid keywords and/or content: details returned |
| 405 | Method Not Allowed | the request used an incorrect method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | the server is not available at the time of the request |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

* * *

Change Log
----------

### Version 0.1 (2021 February)

* Alpha release