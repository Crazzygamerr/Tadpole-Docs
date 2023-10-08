Horizons File API
=================

**Version: 1.0** (2021 July)  
[change log](#change_log)

This API provides access to JPL’s [Horizons system](https://ssd.jpl.nasa.gov/horizons/). An input file must be created containing settings appropriate for the desired ephemeris results. Please see the [Horizons batch example/instructions](https://ssd.jpl.nasa.gov/ftp/ssd/horizons_batch.txt) for details on possible settings. An alternate [Horizons API](/doc/horizons.html) is available that uses the HTTP GET method to send Horizons parameters as query parameters via the URL.

HTTP Request
------------

POST `https://ssd.jpl.nasa.gov/api/horizons_file.api`

### Example Ephemeris Query

Given the following input file called `my_input_file.txt` which requests an observer ephemeris of Mars:

    !$$SOF
    COMMAND='499'
    OBJ_DATA='YES'
    MAKE_EPHEM='YES'
    TABLE_TYPE='OBSERVER'
    CENTER='500@399'
    START_TIME='2006-01-01'
    STOP_TIME='2006-01-20'
    STEP_SIZE='1 d'
    QUANTITIES='1,9,20,23,24,29'
    

#### curl example

Using `curl`, send the `POST` request to the API and save the output in file `results.txt`:

`curl -s -F format=text -F input=@my_input_file.txt https://ssd.jpl.nasa.gov/api/horizons_file.api > results.txt`

#### Python example

Using `python`, send the input file and save the output in `results.txt`:

`python horizons.py my_input_file.txt > results.txt`

where `horizons.py` is as follows:

    import sys
    import requests
    f = open(sys.argv[1])
    url = 'https://ssd.jpl.nasa.gov/api/horizons_file.api'
    r = requests.post(url, data={'format':'text'}, files={'input': f})
    print(r.text)
    f.close()
    

#### Perl example

Using `perl`, send the input file and save the output in `results.txt`:

`perl horizons.pl my_input_file.txt > results.txt`

where `horizons.pl` is as follows:

    use strict;
    use LWP::UserAgent;
    my $file = shift;
    my $ua = LWP::UserAgent->new;
    my $url = 'https://ssd.jpl.nasa.gov/api/horizons_file.api';
    my $res = $ua->post($url,
      Content_Type=>'form-data',
      Content=>[
        'format' => 'text',
        'input' => [ $file ],
      ],
    );
    print $res->content;
    

Query Parameters
----------------

| Parameter | Type | Default | Allowable  <br>Values/Format | Description |
| --- | --- | --- | --- | --- |
| input | string | _none_ | _see description below_ | Horizons input file content, encoded as appropriate |
| format | string | `json` | `json`, `text` | output format: `json` for JSON or `text` for plain-text |

Data Output
-----------

> Please **always check** the JSON payload “signature” object for the API “version”. If the version does not match the version in this document (at the top), there is no guarantee that the format has not changed.
> 
> Example "signature" object with "version" value "1.0": `"signature":{"version":"1.0","source":"NASA/JPL ... API"}`

Successful query requests result in a data payload. The format is controlled by the `format` parameter.

### Example `text`-format Output

The first two lines are added by the API. It is important to note the `API VERSION` value when programmatically processing results to ensure the version is what you expect. The number of blank lines following `API SOURCE` may vary by will be at least one. If you request the body information page (`OBJ_DATA='YES'`), there will be an additional block of text output after the blank lines following `API SOURCE`. The remainder of the output is formatted as provided by the Horizons system.

    API VERSION: 0.2
    API SOURCE: NASA/JPL Horizons API
    
    *******************************************************************************
    Ephemeris / WWW_USER Thu May 14 07:39:14 2020 Pasadena, USA      / Horizons    
    *******************************************************************************
    Target body name: Mars (499)                      {source: mar097}
    Center body name: Earth (399)                     {source: mar097}
    Center-site name: GEOCENTRIC
    *******************************************************************************
    Start time      : A.D. 1998-Jan-01 10:00:00.0000 UT      
    Stop  time      : A.D. 1998-Jan-02 00:00:00.0000 UT      
    Step-size       : 60 minutes
    *******************************************************************************
    Target pole/equ : IAU_MARS                        {West-longitude positive}
    Target radii    : 3396.2 x 3396.2 x 3376.2 km     {Equator, meridian, pole}    
    Center geodetic : 0.00000000,0.00000000,0.0000000 {E-lon(deg),Lat(deg),Alt(km)}
    Center cylindric: 0.00000000,0.00000000,0.0000000 {E-lon(deg),Dxy(km),Dz(km)}
    Center pole/equ : High-precision EOP model        {East-longitude positive}
    Center radii    : 6378.1 x 6378.1 x 6356.8 km     {Equator, meridian, pole}    
    Target primary  : Sun
    Vis. interferer : MOON (R_eq= 1737.400) km        {source: mar097}
    Rel. light bend : Sun, EARTH                      {source: mar097}
    Rel. lght bnd GM: 1.3271E+11, 3.9860E+05 km^3/s^2                              
    Atmos refraction: NO (AIRLESS)
    RA format       : HMS
    Time format     : CAL
    EOP file        : eop.200513.p200804                                           
    EOP coverage    : DATA-BASED 1962-JAN-20 TO 2020-MAY-13. PREDICTS-> 2020-AUG-03
    Units conversion: 1 au= 149597870.700 km, c= 299792.458 km/s, 1 day= 86400.0 s
    Table cut-offs 1: Elevation (-90.0deg=NO ),Airmass (>38.000=NO), Daylight (NO )
    Table cut-offs 2: Solar elongation (  0.0,180.0=NO ),Local Hour Angle( 0.0=NO )
    Table cut-offs 3: RA/DEC angular rate (     0.0=NO )                           
    *****************************************************************************************
     Date__(UT)__HR:MN     R.A._____(ICRF)_____DEC  APmag  S-brt            delta      deldot
    *****************************************************************************************
    $$SOE
     1998-Jan-01 10:00     20 55 41.20 -18 33 23.0   1.17   4.08 2.13799045474771   5.6049390
     1998-Jan-01 11:00     20 55 49.17 -18 32 49.7   1.17   4.08 2.13812533287512   5.6044117
     1998-Jan-01 12:00     20 55 57.13 -18 32 16.5   1.17   4.08 2.13826019831403   5.6038846
     1998-Jan-01 13:00     20 56 05.09 -18 31 43.1   1.17   4.08 2.13839505107206   5.6033579
     1998-Jan-01 14:00     20 56 13.05 -18 31 09.8   1.17   4.08 2.13852989115697   5.6028315
     1998-Jan-01 15:00     20 56 21.01 -18 30 36.5   1.17   4.08 2.13866471857652   5.6023055
     1998-Jan-01 16:00     20 56 28.97 -18 30 03.1   1.17   4.08 2.13879953333835   5.6017797
     1998-Jan-01 17:00     20 56 36.93 -18 29 29.7   1.17   4.08 2.13893433545018   5.6012543
     1998-Jan-01 18:00     20 56 44.89 -18 28 56.3   1.17   4.08 2.13906912492008   5.6007292
     1998-Jan-01 19:00     20 56 52.85 -18 28 22.9   1.17   4.08 2.13920390175678   5.6002045
     1998-Jan-01 20:00     20 57 00.81 -18 27 49.4   1.17   4.08 2.13933866596968   5.5996802
     1998-Jan-01 21:00     20 57 08.76 -18 27 15.9   1.17   4.08 2.13947341756857   5.5991564
     1998-Jan-01 22:00     20 57 16.72 -18 26 42.5   1.17   4.08 2.13960815656331   5.5986329
     1998-Jan-01 23:00     20 57 24.67 -18 26 08.9   1.17   4.08 2.13974288296371   5.5981098
     1998-Jan-02 00:00     20 57 32.63 -18 25 35.4   1.17   4.08 2.13987759677950   5.5975872
    $$EOE
    *****************************************************************************************
    Column meaning:
    
    ... [some content excluded here for brevity] ...
    
     Computations by ...
         Solar System Dynamics Group, Horizons On-Line Ephemeris System
         4800 Oak Grove Drive, Jet Propulsion Laboratory
         Pasadena, CA  91109   USA
         Information: http://ssd.jpl.nasa.gov/
         Connect    : telnet://ssd.jpl.nasa.gov:6775  (via browser)
                      telnet ssd.jpl.nasa.gov 6775    (via command-line)
         Author     : Jon.D.Giorgini@jpl.nasa.gov
    
    *****************************************************************************************
    

If there are any errors in your input file (such as requesting ephemeris output times outside the range available), they will be shown at the end of the normal output. Thus, such error detection is the responsibility of the user.

In some cases, error messages will provide suggestions that are only applicable to the command-line interface. For example, when specifying an observatory code that does not exist, the message suggests `..., ?! for help`. In such cases, if the error is not obvious, please check the [Horizons system documentation](https://ssd.jpl.nasa.gov/horizons/manual.html).

### Example `json`-format Output

When `json`-format is requested, the API attempts to detect any Horizon error message(s), capture them, and report them in the `error` field. Thus, if the `error` field is present in the output, an appropriate programmatic response can be issued.

In the following successful request, the content of interest will be in the JSON `result` field.

    {
      "signature" : {
        "source" : "NASA/JPL Horizons API",
        "version" : "0.2"
      },
      "result" : " \n \n*******************************************************************************\nEphemeris / WWW_USER Thu May 14 08:06:02 2020 Pasadena, USA      / Horizons    \n*******************************************************************************\nTarget body name: Mars (499)                      {source: mar097}\nCenter body name: Earth (399)                     {source: mar097}\nCenter-site name: GEOCENTRIC\n*******************************************************************************\nStart time      : A.D. 1998-Jan-01 10:00:00.0000 UT      \nStop  time      : A.D. 1998-Jan-02 00:00:00.0000 UT      \nStep-size       : 60 minutes\n*******************************************************************************\nTarget pole/equ : IAU_MARS                        {West-longitude positive}\nTarget radii    : 3396.2 x 3396.2 x 3376.2 km     {Equator, meridian, pole}    \nCenter geodetic : 0.00000000,0.00000000,0.0000000 {E-lon(deg),Lat(deg),Alt(km)}\nCenter cylindric: 0.00000000,0.00000000,0.0000000 {E-lon(deg),Dxy(km),Dz(km)}\nCenter pole/equ : High-precision EOP model        {East-longitude positive}\nCenter radii    : 6378.1 x 6378.1 x 6356.8 km     {Equator, meridian, pole}    \nTarget primary  : Sun\nVis. interferer : MOON (R_eq= 1737.400) km        {source: mar097}\nRel. light bend : Sun, EARTH                      {source: mar097}\nRel. lght bnd GM: 1.3271E+11, 3.9860E+05 km^3/s^2                              \nAtmos refraction: NO (AIRLESS)\nRA format       : HMS\nTime format     : CAL \nEOP file        : eop.200513.p200804                                           \nEOP coverage    : DATA-BASED 1962-JAN-20 TO 2020-MAY-13. PREDICTS-> 2020-AUG-03\nUnits conversion: 1 au= 149597870.700 km, c= 299792.458 km/s, 1 day= 86400.0 s \nTable cut-offs 1: Elevation (-90.0deg=NO ),Airmass (>38.000=NO), Daylight (NO )\nTable cut-offs 2: Solar elongation (  0.0,180.0=NO ),Local Hour Angle( 0.0=NO )\nTable cut-offs 3: RA/DEC angular rate (     0.0=NO )                           \n*****************************************************************************************\n Date__(UT)__HR:MN     R.A._____(ICRF)_____DEC  APmag  S-brt            delta      deldot\n*****************************************************************************************\n$$SOE\n 1998-Jan-01 10:00     20 55 41.20 -18 33 23.0   1.17   4.08 2.13799045474771   5.6049390\n 1998-Jan-01 11:00     20 55 49.17 -18 32 49.7   1.17   4.08 2.13812533287512   5.6044117\n 1998-Jan-01 12:00     20 55 57.13 -18 32 16.5   1.17   4.08 2.13826019831403   5.6038846\n 1998-Jan-01 13:00     20 56 05.09 -18 31 43.1   1.17   4.08 2.13839505107206   5.6033579\n 1998-Jan-01 14:00     20 56 13.05 -18 31 09.8   1.17   4.08 2.13852989115697   5.6028315\n 1998-Jan-01 15:00     20 56 21.01 -18 30 36.5   1.17   4.08 2.13866471857652   5.6023055\n 1998-Jan-01 16:00     20 56 28.97 -18 30 03.1   1.17   4.08 2.13879953333835   5.6017797\n 1998-Jan-01 17:00     20 56 36.93 -18 29 29.7   1.17   4.08 2.13893433545018   5.6012543\n 1998-Jan-01 18:00     20 56 44.89 -18 28 56.3   1.17   4.08 2.13906912492008   5.6007292\n 1998-Jan-01 19:00     20 56 52.85 -18 28 22.9   1.17   4.08 2.13920390175678   5.6002045\n 1998-Jan-01 20:00     20 57 00.81 -18 27 49.4   1.17   4.08 2.13933866596968   5.5996802\n 1998-Jan-01 21:00     20 57 08.76 -18 27 15.9   1.17   4.08 2.13947341756857   5.5991564\n 1998-Jan-01 22:00     20 57 16.72 -18 26 42.5   1.17   4.08 2.13960815656331   5.5986329\n 1998-Jan-01 23:00     20 57 24.67 -18 26 08.9   1.17   4.08 2.13974288296371   5.5981098\n 1998-Jan-02 00:00     20 57 32.63 -18 25 35.4   1.17   4.08 2.13987759677950   5.5975872\n$$EOE\n*****************************************************************************************\nColumn meaning:\n \nTIME\n\n  Times PRIOR to 1962 are UT1, a mean-solar time closely related to the\nprior but now-deprecated GMT. Times AFTER 1962 are in UTC, the current\ncivil or \"wall-clock\" time-scale. UTC is kept within 0.9 seconds of UT1\nusing integer leap-seconds for 1972 and later years.\n\n  Conversion from the internal Barycentric Dynamical Time (TDB) of solar\nsystem dynamics to the non-uniform civil UT time-scale requested for output\nhas not been determined for UTC times after the next July or January 1st.\nTherefore, the last known leap-second is used as a constant over future\nintervals.\n\n  Time tags refer to the UT time-scale conversion from TDB on Earth\nregardless of observer location within the solar system, although clock\nrates may differ due to the local gravity field and no analog to \"UT\"\nmay be defined for that location.\n\n  Any 'b' symbol in the 1st-column denotes a B.C. date. First-column blank\n(\" \") denotes an A.D. date. Calendar dates prior to 1582-Oct-15 are in the\nJulian calendar system. Later calendar dates are in the Gregorian system.\n\n  NOTE: \"n.a.\" in output means quantity \"not available\" at the print-time.\n \n R.A._____(ICRF)_____DEC =\n  Astrometric right ascension and declination of the target center with\nrespect to the observing site (coordinate origin) in the reference frame of\nthe planetary ephemeris (ICRF). Compensated for down-leg light-time delay\naberration.\n\n  Units: RA  in hours-minutes-seconds of time   (HH MM SS.ff)\n         DEC in degrees-minutes-seconds of arc  (sDD MN SC.f)\n \n APmag S-brt =\n   Target's approximate apparent visual magnitude & surface brightness. For\nplanets and satellites, values are available only for solar phase angles in the\nrange generally visible from Earth. This is to avoid extrapolation of models\nbeyond their valid (data-based) limits.\n   Units: MAGNITUDE & VISUAL MAGNITUDES PER SQUARE ARCSECOND\n \n delta  deldot =\n   Range (\"delta\") and range-rate (\"delta-dot\") of target center with respect\nto the observer at the instant light seen by the observer at print-time would\nhave left the target center (print-time minus down-leg light-time); the\ndistance traveled by a light ray emanating from the center of the target and\nrecorded by the observer at print-time. \"deldot\" is a projection of the\nvelocity vector along this ray, the light-time-corrected line-of-sight from\nthe coordinate center, and indicates relative motion. A positive \"deldot\"\nmeans the target center is moving away from the observer (coordinate center).\nA negative \"deldot\" means the target center is moving toward the observer.\nUnits: AU and KM/S\n\n\n Computations by ...\n     Solar System Dynamics Group, Horizons On-Line Ephemeris System\n     4800 Oak Grove Drive, Jet Propulsion Laboratory\n     Pasadena, CA  91109   USA\n     Information: http://ssd.jpl.nasa.gov/\n     Connect    : telnet://ssd.jpl.nasa.gov:6775  (via browser)\n                  telnet ssd.jpl.nasa.gov 6775    (via command-line)\n     Author     : Jon.D.Giorgini@jpl.nasa.gov\n\n*****************************************************************************************\n"
    }
    

### Non-unique Object Specification

If the object specified via the `COMMAND` variable in the input file is not unique to the Horizons system, you will get a list of matching objects. Below is an example (`text`-format) output where `COMMAND='DES=141P;'`.

    API VERSION: 0.2
    API SOURCE: NASA/JPL Horizons API
    
    *******************************************************************************
    JPL/DASTCOM            Small-body Index Search Results     2021-May-11 07:53:26
    
     Comet AND asteroid index search:
    
        DES = 141P;
    
     Matching small-bodies:
    
        Record #  Epoch-yr  >MATCH DESIG<  Primary Desig  Name  
        --------  --------  -------------  -------------  -------------------------
        90001032    2011    141P           141P            Machholz 2
        90001033    2019    141P           141P            Machholz 2
        90001034    1994    141P-A         141P-A          Machholz 2
        90001035    1997    141P-A         141P-A          Machholz 2
        90001036    1999    141P-A         141P-A          Machholz 2
        90001037    1994    141P-D         141P-D          Machholz 2
        90001038    1999    141P-D         141P-D          Machholz 2
    
     (7 matches. To SELECT, enter record # (integer), followed by semi-colon.)
    *******************************************************************************
    

In the above example, there are three bodies represented: the parent comet `141P`, fragment A `141P-A`, and fragment D `141P-D`. There are also multiple orbits for each of these comets represented by the `Epoch-yr` field in the results table. To select the object and orbit of interest, you must specify the Horizons record number listed in the `Record #` column. For example, to select the orbit for the parent comet with epoch 2019, specify `COMMAND='90001033;'` in your input file. However, be warned that for small bodies (comets and asteroids) these **record numbers are subject to change without notice**.

For unique comet selection without using Horizons record numbers, there are additional flags that can be used in the `COMMAND` specification. For example, if you know there are fragments for comet 141P and you want the parent comet, you can add the `NOFRAG` flag to the `COMMAND` specification such as `COMMAND='DES=141P;NOFRAG'`. Similarly, if you know you always want the orbit with the latest epoch, you can add the `CAP` flag to the `COMMAND` specification such as `COMMAND='DES=141P;CAP'`. In this example, combining `CAP` and `NOFRAG` will result in a unique match for the parent comet 141P as in `COMMAND='DES=141P;CAP;NOFRAG'`. For fragments, the designation is already unique so you’d only need the `CAP` flag as in `COMMAND='DES=141P-A;CAP'`.

See the [Horizons documentation](https://ssd.jpl.nasa.gov/horizons/manual.html) for more details.

### Small-Body SPK File Generation

The binary SPK file is base-64 encoded on return and, for `text`-format output, begins after the API header text with the base-64 string `REFGL1NQ...` in the following example.

    API VERSION: 0.2
    API SOURCE: NASA/JPL Horizons API
    
    SPK Binary Data Follows -- base64 encoded:
    
    REFGL1NQSyACAAAABgAAAFNNQl9TUEtfRklMRSAgICAgICAgICAgICAgICAgICAgICAgICAgICAg
    ICAgICAgICAgICAgICAgICAgID4AAAA+AAAAULQAAExUTC1JRUVFAAAAAAAAAAAAAAAAAAAAAAAA
    AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
    ...
    AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
    AAAAAAAAAAAAAAAAAAAAAAAAAA==
    

To convert the base-64 encoded content to a usable binary SPK file, extract the encoded portion to a file and use a base-64 decoder. On most Linux systems this is done with the command `base64 --decode {extracted_file_content}`, where `{extacted_file_content}` is the name of the file containing the encoded content from the API.

#### Example SPK Horizons Input Files

##### For Small-Body in the JPL Database

The following input file requests an SPK file spanning from 2020-01-01 through 2030-12-31 (TDB) for asteroids 1 Ceres.

    !$$SOF
    COMMAND='1;'
    EPHEM_TYPE='S'
    START_TIME='2020-01-01'
    STOP_TIME='2030-12-31'
    OBJ_DATA='NO'
    

##### For Small-Body _not_ in the JPL Database

The following input file requests an SPK file spanning from 2030-01-01 through 2031-01-01 (TDB) for the small-body specified with user-supplied osculating elements and non-gravitational parameters

    !$$SOF
     COMMAND     = ';'                 ! Input asteroid/comet NOT in JPL database
     MAKE_EPHEM  = 'YES'               ! Make an ephemeris
     EPHEM_TYPE  = 'S'                 ! Set ephemeris type to SPK
     OBJ_DATA    = 'NO'                ! Suppress object data-sheet
     START_TIME  = '2030-Jan-1'        ! Start time (TDB)
     STOP_TIME   = '2031-Jan-1'        ! Stop time (TDB)
    !
     OBJECT = 'User Apophis'
     EPOCH  = '2459311.5'
     FRAME  = 'J2000'
     EC     = '0.1915350975906659'
     QR     = '0.7458351838564465'
     TP     = '2459424.5839352785'
     OM     = '203.9782743995174'
     W      = '126.6377455281965'
     IN     = '3.33973556589891'
     MA     =
     A      =
     N      =
     RAD    =
    
    ! Next two (H and G) for asteroids only
     H      =
     G      =
    
    ! Next group for comets only
     M1     =
     M2     =
     K1     =
     K2     =
     PHCOF  =
     DT     =
    
    ! Next group for asteroids or comets
     A1     = '3.869431020576E-13'
     A2     = '-2.898628963521E-14'
     A3     =
     R0     = '1.'
     ALN    = '1.'
     NM     = '2.'
     NN     = '5.093'
     NK     = '0.'
     AMRAT  =
    !$$EOF
    

#### Command-line Examples

An example Linux command-line to extract the binary SPK file content for asteroid 1 Ceres from a successful `text`-format output is shown below. However, be advised that such usage will not easily catch errors returned from the API.

    curl -s -F format=text -F input=@my_input_file.txt https://ssd.jpl.nasa.gov/api/horizons_file.api | awk '/REFGL1NQ/,0' | base64 --decode > object.bsp
    

Similarly, for the `json`-format output, the SPK encoded content is contained in the `spk:` object as in the following example:

    {
      "signature" : {
        "source" : "NASA/JPL Horizons API",
        "version" : "0.2"
      },
      "spk_file_id" : "2000001",
      "spk" : "REFGL1NQSyACAAAABgAAAFNNQl9TUEtfRklMRSAgICAgICAgICAgICAgICAgICAgICAgICAgICAg\nICAgICAgICAgICAgICAgICAgID4AAAA+AAA ... AAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAA==\n"
    }
    

Extract the content from the JSON `spk` object excluding the surrounding quotes and then decode to produce the corresponding binary SPK file. The `spk_file_id` can be used as the SPK file’s basename (e.g., `20000001.bsp` in the above example).

#### Sample Scripts

The following scripts expect the Horizons input file as an argument on the command-line. Assuming that input file [see example script above](#script) requests an SPK file for a specific object, the resulting SPK file is decoded and written to a binary file in the current working directory.

##### Perl

    use strict;
    use LWP::UserAgent;
    use JSON;
    use MIME::Base64;
    
    # Define API URL and SPK filename:
    my $url = 'https://ssd.jpl.nasa.gov/api/horizons_file.api';
    my $spk_filename = 'spk_file.bsp';
    
    # Get the Horizons API input file from the command-line:
    my $file = shift;
    die unless ( defined $file and -e $file );
    
    # Build and submit the API request and decode the JSON-response:
    my $ua = LWP::UserAgent->new;
    my $response = $ua->post($url,
      Content_Type=>'form-data',
      Content=>[ 'format'=>'json', 'input'=>[$file] ],
    );
    my $data = decode_json($response->content);
    
    # If the request was valid...
    if ( $response->code eq '200' ) {
      #
      # If the SPK file was generated, decode it and write it to the output file:
      if ( defined $data->{spk} ) {
        #
        # If a suggested SPK file basename was provided, use it:
        if ( defined $data->{spk_file_id} ) { $spk_filename = "$data->{spk_file_id}.bsp"; }
        open (my $fh, ">", $spk_filename) or die "unable to open $spk_filename: $!\n";
        #
        # Decode and write the binary SPK file content:
        print $fh decode_base64($data->{spk});
        close $fh;
        print "wrote SPK content to $spk_filename\n";
        exit;
      }
      #
      # Otherwise, the SPK file was not generated so output an error:
      print "ERROR: SPK file not generated\n";
      if ( defined $data->{result} ) { print $data->{result}; }
      else { print $response->content; }
      exit 1;
    }
    
    # If the request was invalid, extract error content and display it:
    if ( $response->code eq '400' ) {
      print "ERROR in API call: HTTPS response: ", $response->status_line, "\n";
      if ( defined $data->{message} ) { print $data->{message}, "\n"; }
      else { print $response->content; }
      exit 1;
    }
    
    # Otherwise, some other error occurred:
    print "ERROR in API call: HTTPS response: ", $response->status_line, "\n";
    exit 1;
    

##### Python

    import sys
    import json
    import base64
    import requests
    
    # Define API URL and SPK filename:
    url = 'https://ssd.jpl.nasa.gov/api/horizons_file.api'
    spk_filename = 'spk_file.bsp'
    
    # Get the Horizons API input file from the command-line:
    try:
      f = open(sys.argv[1])
    except OSError as err:
      print("Unable to open input file '{0}': {1}".format(sys.argv[1], err))
    
    # Build and submit the API request and decode the JSON-response:
    response = requests.post(url, data={'format':'json'}, files={'input': f})
    f.close()
    try:
      data = json.loads(response.text)
    except ValueError:
      print("Unable to decode JSON results")
    
    # If the request was valid...
    if (response.status_code == 200):
      #
      # If the SPK file was generated, decode it and write it to the output file:
      if "spk" in data:
        #
        # If a suggested SPK file basename was provided, use it:
        if "spk_file_id" in data:
          spk_filename = data["spk_file_id"] + ".bsp"
        try:
          f = open(spk_filename, "wb")
        except OSError as err:
          print("Unable to open SPK file '{0}': {1}".format(spk_filename, err))
        #
        # Decode and write the binary SPK file content:
        f.write(base64.b64decode(data["spk"]))
        f.close()
        print("wrote SPK content to {0}".format(spk_filename))
        sys.exit()
      #
      # Otherwise, the SPK file was not generated so output an error:
      print("ERROR: SPK file not generated")
      if "result" in data:
        print(data["result"])
      else:
        print(response.text)
      sys.exit(1)
    
    # If the request was invalid, extract error content and display it:
    if (response.status_code == 400):
      data = json.loads(response.text)
      if "message" in data:
        print("MESSAGE: {}".format(data["message"]))
      else:
        print(json.dumps(data, indent=2))
    
    # Otherwise, some other error occurred:
    print("response code: {0}".format(response.status_code))
    sys.exit(2)
    

HTTP Response Codes
-------------------

Most errors are returned via appropriate HTTP response codes. However, it is possible to submit Horizons input data (e.g., a bad `START_TIME` value) resulting in Horizons-generated errors. In such cases, a non-error code of 200 is returned so the user is responsible for checking the payload if they wish to detect a such errors.

| HTTP Code | Description | Typical Usage |
| --- | --- | --- |
| 200 | OK  | normal successful result |
| 400 | Bad Request | the request contained invalid keywords and/or content or used a request-method other than GET or POST (details returned in the JSON or text payload) |
| 405 | Method Not Allowed | the request used an incorrect method (see the [HTTP Request](#request) section) |
| 500 | Internal Server Error | the database is not available at the time of the request |
| 503 | Service Unavailable | the server is currently unable to handle the request due to a temporary overloading or maintenance of the server, which will likely be alleviated after some delay |

* * *

Change Log
----------

### Version 1.0 (2020 July)

* Initial release