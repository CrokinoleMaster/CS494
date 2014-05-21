Current Position
================
Displaying current position is not a difficult task. However, there are a few
important features missing in the Google Maps API. For one, it does not provide
a way to show which way the user is facing while using their mobile devices.
Developers are likely to not even consider the web platform when requiring features
like this, and simply choose native instead. But with a little work, we can
implement something that works the same with Javascript.

###Get set up

We will use a small and light library for this guide to keep boilerplate to a
minimum.
<a href="http://hpneo.github.io/gmaps/" target="_blank">gmaps.js</a>
is what we will be using.
You should still be able to follow along even without it since the code is quite
self-explanatory.

Let's first add some boilerplate by creating a function to use as our demo app's
constructor and encapsulate our map object inside.

```javascript
function App(){

    // here is our map object
    var map = new GMaps({div: '#map',
                         lat: -12,
                         lng: -77,
                         mapType: 'SATALLITE'});
}

// start our app when the DOM is ready
document.addEventListener('DOMContentLoaded', function() {
    var app = new App();
});
```
***We will use JQuery from now on so we won't have to show that long document ready
line***

I kept the map object as a private variable because we should not need to directly
access it from the outside.

###Setting up a marker

In order to have a current location marker, we have to first add a marker to the
map. We will use an arbitrary location for now. Later, when the geolocation
callback fires, we can update the marker.

```javascript
// marker icon object
function App(){

    ...

    var currentMarkerIcon =  {path: google.maps.SymbolPath
                                          .FORWARD_CLOSED_ARROW,
                              scale: 4,
                              anchor: new google.maps.Point(0, 3),
                              strokeColor: '#55efcb'};

    // here we create a marker with the icon object at an arbitrary location
    var currentMarker = map.createMarker({ lat: -12, lng: -77, icon: currentMarkerIcon});
}
```
Note that we are using a symbol rather than an image icon. This is because a
symbol is SVG, and simple to rotate. If you want to experiment, feel free to try
rotating an image icon, but it is off topic for this HOW-TO.


###Watching location

Now we are ready to get our location and add our marker!

```javascript
function App(){

    var app = this;

    ...

    // error callback function
    function errorCallback(){ ... }

    this.watchLocation = function() {
        map.addMarker(currentMarker);

        // here we are getting position every second. explanation below.
        window.setInterval(getPosition, 1000);

        function getPosition(){

            // calling the HTML5 geolocation method
            navigator.geolocation.getCurrentPosition(function(position){
              var lat = position.coords.latitude;
              var long = position.coords.longitude;

              // saving our position to a variable
              app.posCurrent = position;

              // set map center
              map.setCenter(lat, long);

              // update current marker position
              currentMarker.setPosition({lat: lat, lng: long});

              // triggering a custom JQuery event so we can manipulate the DOM
              // This is needed since the function is async
              $(app).trigger('move', [app.posCurrent]);

            }, errorCallback, { enableHighAccuracy: true });
        }
    };
}

$(function(){

    ...

    app.watchLocation();
});
```
There are a couple things to mention here.

First, you might ask why we use `navigator.geolocation.getCurrentPosition`, rather
than `navigator.geolocation.watchPosition`. If we use `watchPosition`, we can omit
the `setInterval` method and it might be more efficient. You can certainly use
`watchPosition` instead.

However, from my experience `watchPosition` **does not work**
on devices with no Cellular connection. *What's worse is that it will cause all
other geolocation methods to stop working.* So currently, it is better to use
`getCurrentPosition` instead, and hope they will better implement these HTML5
features in browsers soon.

The JQuery custom event is used so that we could update the DOM when the location
callback is called. If you don't need to show the position, then of course you
wouldn't need this. If you are familiar with JQuery, this will be familar.
The code would look something like this:

```javascript
$(function(){

    ...

    // helper function
    function formatPoint(point) {
        if (point){
          var str = 'lat: ' + point.coords.latitude.toFixed(5) +
                    ' long: ' + point.coords.longitude.toFixed(5);
          return str;
        }
    }

    // JQuery event handler
    $(app).on('move', function(e, posCurrent, /* other variables */) {
        $('#position').html(formatPoint(posCurrent));
    });

})
```

###Getting compass heading

Believe it or not, getting the compass heading is not that difficult with
Javascript. You can find the API for Chrome Android, and Safari Mobile quite
easily. We will make use of a small library so that our code is easier to read.
The library is
<a href="http://ai.github.io/compass.js/" target="_blank">compass.js</a>.
It's a little old,
so if you find that it's out of date, then you can try to contribute.

Let's add a function to our App object to start the compass heading listener.

```javascript
function App(){

    ...

    // add a watchCompass function to our App constructor
    this.watchCompass = function() {

        // start listening to the compass
        Compass.watch(function(heading){

            // let's be cool and take device orientation into account
            if (window.orientation === 90){
                heading += 90;
            } else if (window.orientation === -90){
                heading -= 90;

            // upside down?? just in case someone is weird like that
            } else if (window.orientation === 180){
                heading += 180;
            }
              if (heading > 360){
                heading-=360;
            }
            currentMarkerIcon.rotation = heading;
            currentMarker.set('icon', currentMarkerIcon);
        });
    }

    ...

}

$(function(){

    ...

    // call our method to start updating our Marker!
    app.watchCompass();

    ...

})
```

This part couldn't have been any easier. Because we are using SVG markers,
rotation is as simple as changing the rotation property then updating the marker
icon. We also added orientation checks so that our app feels as good as native.

Try it out on your iPad/iPhone. Worked perfect on mine.

**compass.js did not work on my Nexus 5. Use the HTML5 orientation API directly
to see the results on android**










