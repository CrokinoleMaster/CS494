Algorithms
==========

Calculate distance from a polyline
------------------------------------

This is very useful in an app with a guidance system. Calculating the current
position to a polyline ( the guidance line ) is very common. The calculation is,
like other geolocation calculations, just trigonometry. It requires a few other
functions, which you can use either the Google Maps Geometry Library, or your
own. Writing your own might be better if you want to extend and change so I will
list a few below.

**All functions below have been tested and accuracy is within a meter**

###conversions

Since we will often need to convert degrees and radians, we should define some
conversion functions. We will just go ahead and extend the `Number` prototype

```javascript
// convert degrees to radians
Number.prototype.toRad = function(){
    return this * Math.PI / 180;
};

// convert radians to degrees
Number.prototype.toDegrees = function(){
    return this * 180 / Math.PI;
};
```

###distance to a line

This uses the 'Haversine' formula to calculate distance between two coordinates

```javascript
// a and b are html5 geolocation coordinates
// RETURN distance in km
function getPntDist(a, b){

    // earth's radius
    var R = 6371;

    // change these if using other coordinate objects
    var lat1 = a.coords.latitude;
    var lat2 = b.coords.latitude;
    var lng1 = a.coords.longitude;
    var lng2 = b.coords.longitude;

    // trig calculations need to be done using radians rather than degrees
    var dLat = (lat2 - lat1).toRad();
    var dLng = (lng2 - lng1).toRad();

    // main formula here
    var a = Math.sin(dLat / 2) * Math.sin(dLat /2) +
            Math.cos(lat1.toRad()) * Math.cos(lat2.toRad()) *
            Math.sin(dLng / 2) * Math.sin(dLng / 2);
    var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    var d = R * c;
    return d;
}
```
###get bearing with start and end points

The bearing is important to know which direction you are heading in. Keep in
mind that this function returns bearing in radians.

```javascript
// a and b are html5 geolocation coordinates
// RETURN bearing in radians
function getBearing(a, b){

    // alter these if using other coordinate objects
    var lat1 = a.coords.latitude.toRad();
    var lat2 = b.coords.latitude.toRad();
    var lng1 = a.coords.longitude.toRad();
    var lng2 = b.coords.longitude.toRad();

    // main formula
    var y = Math.sin(lng2 - lng1) * Math.cos(lat2);
    var x = Math.cos(lat1) * Math.sin(lat2) -
            Math.sin(lat1) * Math.cos(lat2) * Math.cos(lng2 - lng1);
    var brng = Math.atan2(y, x);
    return brng;
}
```

###actual function to get distance to polyline

The function to get the perpendicular distance to polyline using the above
functions.

```javascript
// RETURN distance in km
function getTrackDist(a, b, curr){
    var R = 6371;

    // distance between current location and point a
    dist13 = getPntDist(a, curr);

    // bearing from a to current
    brng13 = getBearing(a, curr);

    // bearing from a to b
    brng12 = getBearing(a, b);

    // calculate distance
    var crossDist = Math.asin(Math.sin(dist13 / R ) * Math.sin(brng13 - brng12)) * R;
    return crossDist;
}
```

With these simple functions, you can do many calculations needed for your web
app.
For all the algorithms you might need you can check:

<a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">movable-type</a>

You can use the example code, but I prefer
to write my own using the formulas provided as reference.
