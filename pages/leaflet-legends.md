Leaflet Dynamic Legends
=======================
*This guide assumes that you have a very basic understanding of
<a href="http://leafletjs.com/" target="_blank">leaflet</a>*

*If you have not used leaflet before this guide will not help.*

Leaflet legends are great, but leaflet doesn't come with a way to switch legends.
You often find that you have multiple overlays for one map, and each requires a
different legend. This guide will show how to create dynamic legends so that only
the legends for the currently visible overlays are displaying. Toggling the overlays
will automatically update the legends being displayed.


First let's create a function to add legends.
```javascript
// app is the object we are using to namespace
app.addLegend = function( value, name ){
    var legend = L.control({ position: 'bottomleft' });

    // we are using a helper function, you can see below
    legend.onAdd = leafletLegendHelpers.getOnAddArrayLegend(value, 'legend');

    // assuming app.map is the already created leaflet map
    legend.addTo(app.map);
    legend.name = name;

    // app.legends is an array to keep track of all the legends for the different
    // overlays that are currently visible
    app.legends.push(legend);
}

```

Next we define the overlay add callback which gets fired when an overlay is toggled
to visible.
```javascript
app.map.on('overlayadd', function(eventLayer){

    // assuming our overlays data is stored at app.map.layers.overlays
    var overlays = app.map.layers.overlays;

    // we are going to use the JQuery map function here
    // to map the overlays into array
    overlays = $.map(overlays, function(val, i){
        return [val];
    })

    // with overlays as array, we can filter to find the overlay with the same
    // name as the one being added. overlay will consist of only the one that was
    // added
    var overlay = overlays.filter(function(o){
        return 0.name == eventLayer.name;
    })

    // now we can add the legend using the addLegend function we created
    // assuming each overlay object has a legend property which is the legend
    // object
    if (overlay[0]) app.addLegend(overlay[0].legend, overlay[0].name);
});
```

Now we just need a callback for when we toggle an overlay to not visible
```javascript
app.map.on('overlayremove', function(eventLayer){

    // if there are currently visible legends
    if (app.legends[0]) {

        // loop through the currently visible legends
        // if the legend's name is the same as the overlay being removed
        // remove the legend from the map
        // and remove the legend from the current legends array
        app.legends.forEach(function(l,i){
            if (l.name == eventLayer.name){
                l.removeFrom(map);
                app.legends.splice(i, 1);
            }
        });
    }
});
```

With a few lines of code, we now have leaflet legends that are dynamic!
