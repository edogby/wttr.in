*wttr.in — the right way to check the weather.*

wttr.in is a console oriented weather forecast service, that supports various information representation methods
like terminal oriented ANSI-sequences for console HTTP clients (curl, httpie, or wget),
HTML for web browsers, or PNG for graphical viewers. 
wttr.in uses [wego](http://github.com/schachmat/wego) for visualization
and various data sources for weather forecast information.

You can check it at [wttr.in](http://wttr.in).

## Usage

You can access the service from a shell or from a Web browser:

    $ curl wttr.in
    Weather for City: Paris, France

         \   /     Clear
          .-.      10 – 11 °C     
       ― (   ) ―   ↑ 11 km/h      
          `-’      10 km          
         /   \     0.0 mm         


That is how the actual weather report for your location looks like (it is live!):

![Weather Report](http://wttr.in/MyLocation.png?)

(it's not your location actually. GitHub's CDN hides your real IP address with its own IP address,
but it is still a live weather report in your language).

You can specify the location that you want to get the weather information for.
If you omit the location name, you will get the report for your current location,
based on your IP address.

    $ curl wttr.in/London
    $ curl wttr.in/Moscow

Use 3-letters airport codes in order to get the weather information
at a certain airport:

    $ curl wttr.in/muc      # Weather for IATA: muc, Munich International Airport, Germany
    $ curl wttr.in/ham      # Weather for IATA: ham, Hamburg Airport, Germany

If you want to use some geographical location (not just a town or a city)
(e.g. it can be a site in a city, a mountain name, a special location, etc.) you should place `~` before its name.
That means the location name should be looked up before:

	$ curl wttr.in/~Vostok+Station
	$ curl wttr.in/~Eiffel+Tower
	$ curl wttr.in/~Kilimanjaro

In this case there is a line below the weather forecast output describing the founded precise position:

	Location: Vostok Station, станция Восток, AAT, Antarctica [-78.4642714,106.8364678]
    Location: Tour Eiffel, 5, Avenue Anatole France, Gros-Caillou, 7e, Paris, Île-de-France, 75007, France [48.8582602,2.29449905432]
	Location: Kilimanjaro, Northern, Tanzania [-3.4762789,37.3872648] 

You can also use IP-addresses (direct) or domain names (prefixed with @)
as a location specificator:

    $ curl wttr.in/@github.com
    $ curl wttr.in/@msu.ru

To get detailed information online, you can access the [/:help](http://wttr.in/:help) page:

    $ curl wttr.in/:help

## Additional options

By default the USCS units are used for the queries from the USA and the metric system for the rest of the world.
You can override this behavior with the following options:

    $ curl wttr.in/Amsterdam?u
    $ curl wttr.in/Amsterdam?m

## Supported formats

wttr.in supports three output formats at the moment:

* ANSI for the terminal;
* HTML for the browser;
* PNG for the graphical viewers.

The ANSI and HTML formats are selected basing on the User-Agent string.
The PNG format can be forced by adding `.png` to the end of the query:

    $ wget wttr.in/Paris.png

You can use all of the options with the PNG-format like in an URL, but you have
to separate them with `_` instead of `?` and `&`:

    $ wget wttr.in/Paris_0tqp_lang=fr.png

Useful options for the PNG format:

* `t` for transparency (`transparency=150`);
* transparency=0..255 for a custom transparency level.

Transparency is a useful feature when the weather PNGs are used to 
add weather data to the pictures:

    $ convert 1.jpg <( curl wttr.in/Oymyakon_tqp0.png ) -geometry +50+50 -composite 2.jpg

Here:

* `1.jpg` - source file;
* `2.jpg` - target file;
* Oymyakon - name of the location;
* tqp0 - options (recommended).

![Picture with weather data](https://pbs.twimg.com/media/C69-wsIW0AAcAD5.jpg)

## Special pages

wttr.in can be used not only to check the weather:

    $ curl wttr.in/Moon

To see the current Moon phase (uses [pyphoon](https://github.com/chubin/pyphoon) as its backend).

    $ curl wttr.in/Moon@2016-12-25

To see the Moon phase for the specified date (2016-12-25).

## Internationalization and localization

wttr.in supports multilingual locations names: they can be specified in any language in the world
(it may be surprising, but many locations in the world do not have any English name at all).

The query string should be specified in Unicode (hex encoded or not). If it contains spaces
they must be replaced with +:

    $ curl wttr.in/станция+Восток
    Weather report: станция Восток

                   Overcast
          .--.     -65 – -47 °C
       .-(    ).   ↑ 23 km/h
      (___.__)__)  15 km
                   0.0 mm

The language used for the output (except the location name) does not depend on the input language
and it is either English (by default) or the preferred language of the browser (if the query
was issued from a browser) that is specified in the query headers (`Accept-Language`).

It can be set explicitly when using console clients by means of the appropriate command line options
(for example: `curl -H "Accept-Language: fr" wttr.in` or `http GET wttr.in Accept-Language:ru`).

The preferred language can be forced using the `lang` option:

    $ curl wttr.in/Berlin?lang=de

The third option is to choose the language using DNS name used in the query:

    $ curl de.wttr.in/Berlin

wttr.in is currently translated into 54 languages and the number of supported languages
is constantly growing.

See [/:translation](http://wttr.in/:translation) to learn more about the translation process, 
to see the list of supported languages and contributors, or to know how you can help to translate wttr.in in your language.

![Queries to wttr.in in various languages](https://pbs.twimg.com/media/C7hShiDXQAES6z1.jpg)

## Installation 

To install the program:

1. Install external dependencies
2. Install python dependencies used by the service
3. Get WorldWeatherOnline API Key
4. Configure wego
5. Configure wttr.in
6. Configure HTTP-frontend service

### Install external dependencies

External requirements:

* [wego](https://github.com/schachmat/wego), weather client for terminal

To install `wego` you must have [golang](https://golang.org/doc/install) installed. After that:

    $ go get -u github.com/schachmat/wego
    $ go install github.com/schachmat/wego

### Install python dependencies

Python requirements:

* Flask
* geoip2
* geopy
* requests
* gevent

If you want to get weather reports as PNG files, install also:

* PIL
* pyte (>=0.6)
* necessary fonts

You can install most of them using `pip`. 

If `virtualenv` is used:

    $ virtualenv ve
    $ . ve/bin/activate
    $ ve/bin/pip install -r requirements.txt
    $ ve/bin/python bin/srv.py

Also, you need to install the geoip2 database.
You can use a free database GeoLite2, that can be downloaded from http://dev.maxmind.com/geoip/geoip2/geolite2/

### Get WorldWeatherOnline key

To get the WorldWeatherOnline API key, you must register here:
 
    https://developer.worldweatheronline.com/auth/register

### Configure wego

After you have the key, configure `wego`:

    $ cat ~/.wegorc 
    {
        "APIKey": "00XXXXXXXXXXXXXXXXXXXXXXXXXXX",
        "City": "London",
        "Numdays": 3,
        "Imperial": false,
        "Lang": "en"
    }

The `City` parameter in `~/.wegorc` is ignored.

### Configure wttr.in

Configure the following environment variables specifing the path to the local `wttr.in`
installation, to the GeoLite database and to the `wego` installation. For example:

    export WTTR_MYDIR="/home/igor/wttr.in"
    export WTTR_GEOLITE="/home/igor/wttr.in/GeoLite2-City.mmdb"
    export WTTR_WEGO="/home/igor/go/bin/wego"
    export WTTR_LISTEN_HOST="0.0.0.0"
    export WTTR_LISTEN_PORT="8002"


### Configure HTTP-frontend service

Configure the web server, that will be used
to access the service (if you want to use a web frontend; it's recommended):

    server {
        listen [::]:80;
        server_name  wttr.in *.wttr.in;
        access_log  /var/log/nginx/wttr.in-access.log  main;
        error_log  /var/log/nginx/wttr.in-error.log;

        location / {
            proxy_pass         http://127.0.0.1:8002;

            proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr;
            proxy_set_header   X-Forwarded-For  $remote_addr;

            client_max_body_size       10m;
            client_body_buffer_size    128k;

            proxy_connect_timeout      90;
            proxy_send_timeout         90;
            proxy_read_timeout         90;

            proxy_buffer_size          4k;
            proxy_buffers              4 32k;
            proxy_busy_buffers_size    64k;
            proxy_temp_file_write_size 64k;

            expires                    off;
        }
    }


