title: Deta + RealTime Tracking - Part 4
date: 2021-02-11
descr: Realtime position Tracking using deta and micros
tags: [awesome, realtime, geoposition]

For the last part of this serie, we have to implement a frontend for our service, this can be achieved in various ways but we are going to make a web app and use a javascript library called mapbox, but first we have to connect the back with front.

Edit main.py to insert new modules and some mods.

```bash
from fastapi import FastAPI, Request
from fastapi.templating import Jinja2Templates
from fastapi.responses import HTMLResponse
from deta import Deta

KEY = 'project key'
BASE = 'realtime' # detaBase name

deta = Deta(KEY) # configure your Deta project
db = deta.Base(BASE)  # access your DB

#Templates Folder
templates = Jinja2Templates(directory="templates")

app = FastAPI()

@app.get('/')
async def hellow_world():
	return {'hola':'mundo'}

# endpoint
@app.get('/deta')
async def deta():
    # get database entry, return a list. 
    # in this case return a dictionary inside a list
    resultado = next(db.fetch())
    
    # iterate for desired data inside the list
    for item in resultado:
      lat = item['lat'] # latitude
      lon = item['lon'] # longitude

    # data to be retrieved in the browser  
    return {"geometry": {"type": "Point", "coordinates": [lon, lat]}, "type": "Feature", "properties": {}}
    
#MapBox template
@app.get("/map", response_class=HTMLResponse, include_in_schema=False)
async def map(request: Request):
    return templates.TemplateResponse("mbox.html",{"request":request})
```

Now create a `templates` folder and inside create a html file called `mbox.html`
```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8" />
    <title>Realtime deta</title>
    <meta name="viewport" content="initial-scale=1,maximum-scale=1,user-scalable=no" />
    <script src="https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js"></script>
    <link href="https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css" rel="stylesheet" />
    <style>
        body {
            margin: 0;
            padding: 0;
        }

        #map {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 100%;
        }
    </style>
</head>

<body>
    <div id="map"></div>

    <script>
        mapboxgl.accessToken =
            'pk.eyJ1IjoibWFyazYyNzI2IiwiYSI6ImNqc2k2M3gyZjBoZG40NGxwNmNoYjh0aHoifQ.0Tv0shaBZSZVxG1xbT9LLQ';
        var map = new mapboxgl.Map({
            container: 'map',
            style: 'mapbox://styles/mapbox/streets-v11',
            zoom: 11,
            center: [-62.69133, 8.32179]
        });

        var url = 'https://deta-domain/deta';
        map.on('load', function () {
            var request = new XMLHttpRequest();
            window.setInterval(function () {
                // make a GET request to parse the GeoJSON at the url
                request.open('GET', url, true);
                request.onload = function () {
                    if (this.status >= 200 && this.status < 400) {
                        // retrieve the JSON from the response
                        var json = JSON.parse(this.response);
                        console.log(json)

                        // update the drone symbol's location on the map
                        map.getSource('drone').setData(json);

                        // fly the map to the drone's current location
                        map.flyTo({
                            center: json.geometry.coordinates,
                            zoom: 17,
                            speed: 0.5
                        });
                    }
                };
                request.send();
            }, 2000);

            map.addSource('drone', {
                type: 'geojson',
                data: url
            });

            map.addLayer({
                'id': 'drone',
                'type': 'symbol',
                'source': 'drone',
                'layout': {
                    'icon-image': 'rocket-15'
                }
            });
        });
    </script>

</body>

</html>
```
In var `url = 'https://deta-domain/deta';` put your deta micros link

Now navigate to `https://deta-domain/map` to see if all works.

This is my final example [Realtime Map](https://2bbwzl.deta.dev/mbox)

This was a two month of research and learning on the way, i'm not close to be a good developer but i've really enjoyed this journey. If you have some question, dropme a tweet.

For further read, take a look at.

* [Mapbox GL](https://www.mapbox.com/mapbox-gljs)
* [Deta](https://deta.sh)
* [FastAPI](https://fastapi.tiangolo.com/)
* [GeoJson](https://geojson.org/)
