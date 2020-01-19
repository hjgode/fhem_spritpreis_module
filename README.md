# Spritpreismodule.pm
>mod by hjgode

an experimental module to provide fuel price updates from various apis
my main motivation to start this is to gain some experience writing fhem modules from scratch.
it's not primarily thought of as a production ready module and things may vary. a lot.

### Installation
####Manually
copy 72_Spritpreis.pm to /opt/fhem/FHEM 
then restart fhem

####Automatic

    update all https://raw.githubusercontent.com/hjgode/fhem_spritpreis_module/master/controls_spritpreis.txt

### Contributions
Contributions are welcome

### Branches
* Master: whatever I deem slightly useful
* Develop: a slightly useful branch (not all functions may be working) that contributors should fork and provide their pull requests to


### ToDo:
General architecture:
There are several providers of fuel price services in various countries. This module should provide "branded" subs for those providers (like Spritpreis_Tankerkoenig_getPricesForLocation(@) etc). The module itself would be configured with 
define Benzinpreis Spritpreis <provider> <additional provider relevant parameters>

[ ] the Tankerkönig configurator spits out a pretty useful bit of json, it would be great to just use that to get the IDs


### Links

* Tankerkönig: https://creativecommons.tankerkoenig.de/ (DE, API for location and ID driven search)
* Spritpreisrecher: http://www.spritpreisrechner.at (AT, API for rectangular search are, possibly more)
    * Examples for Spritpreisrechner:  https://blog.muehlburger.at/2011/08/spritpreisrechner-at-apps-entwickeln and http://gregor-horvath.com/spritpreis.html

#HELP

# FHEM Spritpreis Modul

Name: 72_Spritpreis.pm

## Installation

Copy modul file 72_Spritpreis.pm to FHEM directory. libjson-perl should be installed for perl. Enter cmd "reload 72_Spritpreis.pm" in fhem web or restart fhem.

## Add new device for Spritprpeis

In fhem web cmd enter: "define spritpreis Spritpreis Tankerkoenig 0000-0000-...", where 0000-0000-... is your api key you got from https://creativecommons.tankerkoenig.de/

## Add new station

To watch the prices for a station, locate the station ID with the help of https://creativecommons.tankerkoenig.de/TankstellenFinder/index.html or the current helper at https://creativecommons.tankerkoenig.de/. For example an Aral station in Neuss gives following tankerkoenig station information:

    [
      {
        "id": "127035c1-a7c7-41db-9976-ab4cd14b7271",
        "name": "Aral Tankstelle",
        "brand": "ARAL",
        "street": "Engelbertstraße",
        "house_number": "",
        "post_code": 41462,
        "place": "Neuss",
        "lat": 51.2071037,
        "lng": 6.671111,
        "isOpen": true
      }
    ]  

Add the station ID by using the set function inside the detail view of the Spritpreis device you have created:

    set <device_name> add id 127035c1-a7c7-41db-9976-ab4cd14b7271

This will add the station and update the readings in the Spritpreis device.

Alternately you can add the station ID as an attribute using

    attr <device_name> IDs "127035c1-a7c7-41db-9976-ab4cd14b7271"

and then use "set <device_name>test" in web cmd view.</device_name>

The readings will show prefixed by the order number of the added IDs. For example:

    0_brand
    0_e10_price
    ...
    1_brand
    1_e10_price
    ...

## Reference

The source type Spritpreisrechner is not implemented, does only support geolocations. Only Tankerkoenig is implemented.

define _device_name_ Spritpreis _price_source_ _api_key_

> device_name
> 
> > name of the newly created fhem device
> 
> price_source
> 
> > either Tankerkoenig or ~~Spritpreisrechner~~
> > 
> > for Tankerkoenig the <api-key> argument is mandatory, ~~for Spritpreisrechner no additional argument needed~~
> 
> api-key: the api key you got from Tankerkoenig

### set<device_name></device_name>

#### update

> update one or more station readings provided by their station IDs
> 
> > -none-:
> > 
> > > default, if no argument is given, all stations are updated
> > 
> > id <station_id(s)>
> > 
> > examples
> > 
> > > set <device_name>update</device_name>
> > > 
> > > > updates all defined stations
> > > 
> > > set <device_name>update id</device_name>
> > > 
> > > > updates all defined stations
> > > 
> > > set <device_name>update id 127035c1-a7c7-41db-9976-ab4cd14b7271</device_name>
> > > 
> > > set <device_name>update id 127035c1-a7c7-41db-9976-ab4cd14b7271,12121212-1212-1212-1212-121212121212</device_name>
> 
> all
> 
> > updates the reading for all defined station IDs
> > 
> > example
> > 
> > > set <device_name>update all</device_name>

#### add

> add id<station_id></station_id>
> 
> > adds the station with ID to the list of stations

#### delete

> not implemented yet

### get

#### search

> Google location search is not usable without an api-key  
> example: set _device_name_ search _lat_ _lon_ _rad_  
> where location is lat/lon and search radius in km provided as _rad_

#### test

> will populate the internal station ID list by the IDs entered as attribut

## attributs

### IDs

> list of IDs to be used, will update internal list, when fhem starts or when set <device_name>test is used</device_name>

### interval

> how often the data is requested in minutes interval, please do not stress the host! Default is 15 minutes.

### lat

> not used

### lon

> not used

### rad

> not used

### type

> not used: which prices are of interrest
>     
> 
> > e5
> >     e10
> >     diesel
> >     all

#### sortby

> not used

#### apikey

> not used, see define

#### address

    not used    

#### priceformat

> 2dezCut
> 
> > cut decimals after two digits
> 
> 2dezRound
> 
> > round decimal to tow digits
> 
> 3dez
> 
> > report three decimal digits


