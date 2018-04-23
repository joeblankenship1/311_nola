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

The 311 Calls data set was huge and significantly affected load time. To reduce the size of the file, we used a `SQL` statement with `ogr2ogr` to select the three features we wanted to map:

```
ogr2ogr -f "GeoJSON" -where ""

### Data

* [311 Calls (2012-Present)](https://data.nola.gov/City-Administration/311-Calls-2012-Present-/3iz8-nghx)
* [Neighborhood Statistical Areas](https://data.nola.gov/Geographic-Base-Layers/Neighborhood-Statistical-Areas/c2j2-5qdf)
* [Orleans Parish Boundary](https://data.nola.gov/dataset/Orleans-Parish-Boundary/5jjm-ygfn)
* [New Orleans Urban Area](https://www.census.gov/geo/maps-data/data/cbf/cbf_ua.html)

The content on the pages is licensed under a [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-nc-sa/4.0/).
