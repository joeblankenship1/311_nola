# 311 Calls (2017)

This web app represents calls to the City of New Orleans' 311 Call Center in 2017.

### CSV to GeoJSON with ogr2ogr

In order to convert our 311 calls CSV to a GeoJSON with the proper projection, you must create a `.vrt` file as configured below:

```
<OGRVRTDataSource>
    <OGRVRTLayer name="311_calls_new">
        <SrcDataSource>311_calls_new.csv</SrcDataSource>
        <GeometryType>wkbPoint</GeometryType>
        <LayerSRS>WGS84</LayerSRS>
        <GeometryField encoding="PointFromColumns" x="longitude" y="latitude"/>
    </OGRVRTLayer>
</OGRVRTDataSource>
```

Notice that we declare a layer name, the input CSV, our reference system (WGS84), and our longitude and latitude.

You can now save this as `inputFile.vrt` and then run your `ogr2ogr` command:

```
ogr2ogr -f GeoJSON outputFile.json inputFile.vrt
```

### Extract features with ogr2ogr

Both the Urban Areas and 311 Calls data sets needed to be paired down to make visualization simple and improve load time. The Urban Areas data set includes the footprints of every urban area in the USA. Since we are only interested in New Orleans, we selected all relevant features and created a new GeoJSON file using the `ogr2ogr` command:

```
ogr2ogr -f "GeoJSON" -where "NAME10='New Orleans, LA'" nola-urban.json urban-areas.json
```

The 311 Calls data set was huge and significantly affected load time. To reduce the size of the file, we used `ogr2ogr` to select the three features we wanted to map:

```
ogr2ogr -f "GeoJSON" -where "issue_type='Street Light' OR issue_type='Sidewalk Repair' OR issue_type='Rodent Complaint'" 311_calls_2017_selected.json 311_calls_2017.json
```

### Style circleMarkers

To style the circleMarkers according to issue type, we first created style option variables for each type. We picked the colors for each type using a triadic color scheme generated by a [color picker](https://htmlcolorcodes.com/color-picker/). For example:

```
var streetLightOptions = {
    radius: 4,
    fillColor: "#30DB7A",
    color: "#000",
    weight: 1,
    opacity: 1,
    fillOpacity: 0.8
};
```
Next, we altered the style of the 311 layer as it gets added to the map. We differentiated the types and then assigned the options we created earlier to the appropriate type:

```
style: function(feature) {
    // shortcut to variable
    var type = feature.properties.issue_type;

    // assign options
    return type === 'Street Light' ? streetLightOptions :
        type === 'Sidewalk Repair' ? sidewalkOptions : rodentOptions;
}
```



### Data

* [311 Calls (2012-Present)](https://data.nola.gov/City-Administration/311-Calls-2012-Present-/3iz8-nghx)
* [Neighborhood Statistical Areas](https://data.nola.gov/Geographic-Base-Layers/Neighborhood-Statistical-Areas/c2j2-5qdf)
* [Orleans Parish Boundary](https://data.nola.gov/dataset/Orleans-Parish-Boundary/5jjm-ygfn)
* [New Orleans Urban Area](https://www.census.gov/geo/maps-data/data/cbf/cbf_ua.html)

The content on the pages is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-nc-sa/4.0/).
