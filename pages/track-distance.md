Using Our Simple Setup
======================
We can easily use our simple setup for other functionalities.
The previous articles went over the basic algorithms and how to get current
position. We can use some of the things from there to easily calculate the
perpendicular distance between current position and a polyline in "real-time".

In a `watchLocation` function like the one in the previous section, we can simply
call the function from the algorithms page.

```javascript
this.watchLocation = function(){

    ...

    window.setInterval(getPosition, 1000);
    function getPosition(){
        navigator.geolocation.getCurrentPosition(function(position){

            ...

            // assuming we made a polyline with pointA and pointB
            if (pointA && pointB){
                app.trackDist = getTrackDist(pointA, pointB, app.posCurrent);
            }
            // here you can do whatever you want with trackDist
            // we will trigger a JQuery event
            $(app).trigger('move', [app.trackDist, /* other variables to pass */]);

        }, errorCallback, { enableHighAccuracy: true });
    }
};

```

From here you can update the DOM, or do something to the map. Other calculations
can be done in the same way. This keeps data up to date with your current location.


