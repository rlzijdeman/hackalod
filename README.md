# HackaLOD 2018

Code, data and tools for HackaLOD 2018 hackathon, team __Hic Sunt Leones__:

- [Menno den Engelse](http://www.islandsofmeaning.nl/)
- [Bert Spaan](http://bertspaan.nl)

Goal: make it easier to browse and find [georectified historical maps](http://beeldbank.amsterdam.nl/beeldbank/indeling/grid?f_string_geoserver_store%5B0%5D=%2A) from the Amsterdam Municipal Archives.

## Results

### Map Area Data

- [Map area data](https://github.com/bertspaan/hackalod/blob/master/data/all-masks.geojson)

[![](geojson.png)](https://github.com/bertspaan/hackalod/blob/master/data/all-masks.geojson)

### Map Me

- [Map Me](http://lab.adamlink.nl/mapme/index.html)

[![](mapme.png)](http://lab.adamlink.nl/mapme/index.html)

Map area data is available via the [Adamlink SPARQL endpoint](https://data.adamlink.nl/menno/alles/services/alles#). [SPARQL it yourself](https://druid.datalegend.net/AdamNet/all/sparql/endpoint#query=PREFIX%20bif%3A%20%3Chttp%3A%2F%2Fwww.openlinksw.com%2Fschemas%2Fbif%23%3E%0APREFIX%20dc%3A%20%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Felements%2F1.1%2F%3E%0APREFIX%20dct%3A%20%3Chttp%3A%2F%2Fpurl.org%2Fdc%2Fterms%2F%3E%0APREFIX%20geo%3A%20%3Chttp%3A%2F%2Fwww.opengis.net%2Font%2Fgeosparql%23%3E%0APREFIX%20sem%3A%20%3Chttp%3A%2F%2Fsemanticweb.cs.vu.nl%2F2009%2F11%2Fsem%2F%3E%0APREFIX%20foaf%3A%20%3Chttp%3A%2F%2Fxmlns.com%2Ffoaf%2F0.1%2F%3E%0APREFIX%20wdt%3A%20%3Chttp%3A%2F%2Fwww.wikidata.org%2Fprop%2Fdirect%2F%3E%0APREFIX%20xsd%3A%20%3Chttp%3A%2F%2Fwww.w3.org%2F2001%2FXMLSchema%23%3E%0A%0Aselect%20%3Fkaart%20%3Fimg%20%3Fx%20%3Fy%20%3Ftitle%20%7B%0A%20%20%3Fkaart%20dct%3Aspatial%20%3Fspatial%20.%0A%20%20%3Fkaart%20foaf%3Adepiction%20%3Fimg%20.%0A%20%20%3Fkaart%20dc%3Atitle%20%3Ftitle%20.%0A%20%20%3Fspatial%20dc%3Atype%20%22outline%22%5E%5Exsd%3Astring%20.%0A%20%20%3Fspatial%20geo%3AhasGeometry%2Fgeo%3AasWKT%20%3Fwktmap%20.%0A%20%20%3Fspatial%20wdt%3AP2046%20%3Fkm2%20.%0A%20%20bind%20(bif%3Ast_geomfromtext(%22POINT(4.895168%2052.370216)%22)%20as%20%3Fx)%0A%20%20bind%20(bif%3Ast_geomfromtext(%3Fwktmap)%20as%20%3Fy)%0A%20%20FILTER%20(bif%3Ast_intersects(%3Fx%2C%20%3Fy))%0A%7D%0AORDER%20BY%20ASC(%3Fkm2)%0Alimit%205&endpoint=https%3A%2F%2Fdruid.datalegend.net%2F_api%2Fdatasets%2FAdamNet%2Fall%2Fservices%2Fendpoint%2Fsparql&requestMethod=POST&tabTitle=Query&headers=%7B%7D&contentTypeConstruct=application%2Fn-triples%2C*%2F*%3Bq%3D0.9&contentTypeSelect=application%2Fsparql-results%2Bjson%2C*%2F*%3Bq%3D0.9&outputFormat=table) with this example query:

```sparql
PREFIX bif: <http://www.openlinksw.com/schemas/bif#>
PREFIX dc: <http://purl.org/dc/elements/1.1/>
PREFIX dct: <http://purl.org/dc/terms/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX sem: <http://semanticweb.cs.vu.nl/2009/11/sem/>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

select ?kaart ?img ?x ?y ?title {
  ?kaart dct:spatial ?spatial .
  ?kaart foaf:depiction ?img .
  ?kaart dc:title ?title .
  ?spatial dc:type "outline"^^xsd:string .
  ?spatial geo:hasGeometry/geo:asWKT ?wktmap .
  ?spatial wdt:P2046 ?km2 .
  bind (bif:st_geomfromtext("POINT(4.895168 52.370216)") as ?x)
  bind (bif:st_geomfromtext(?wktmap) as ?y)
  FILTER (bif:st_intersects(?x, ?y))
}
ORDER BY ASC(?km2)
limit 5
```

### Amsterdam op de Kaart

- [Amsterdam op de Kaart](http://bertspaan.nl/amsterdam-op-de-kaart)

[![](https://raw.githubusercontent.com/bertspaan/amsterdam-op-de-kaart/master/screenshot.png)](http://bertspaan.nl/amsterdam-op-de-kaart)

## Scripts

1. [`scrape-beeldbank.js`](scripts/scrape-beeldbank.js): scrape [all georectified maps from the Beeldbank](http://beeldbank.amsterdam.nl/beeldbank/indeling/grid?f_string_geoserver_store%5B0%5D=%2A)
2. [`create-links.js`](scripts/create-links.js): save permalinks and UUIDs, merge with CSV data
3. [`download-tiffs.sh`](scripts/download-tiffs.sh): download all GeoTIFFs from WMS server
4. [`extract-masks.sh`](scripts/extract-masks.sh): use GDAL to turn GeoTIFFs into GeoJSON
5. [`to-spacetime.js`](scripts/to-spacetime.js): convert to [NYC Space/Time Directory](http://spacetime.nypl.org) data format
6. [`feature-collection.js`](scripts/feature-collection.js): create GeoJSON file with all masks
7. [`group-maps.js`](scripts/group-maps.js): group all maps in groups of 25 years

## Example links

  - Thumbnails:
    - http://images.memorix.nl/ams/thumb/140x140/de61435f-d219-0f32-8930-3ec48422fcea.jpg
    - http://images.memorix.nl/ams/thumb/1000x1000/1fc4dfdc-e92c-6f65-2f3a-e960f9e52222.jpg
  - GeoTIFF:
    - http://geoserver.memorix.nl/geoserver/ams/wcs?service=WCS&version=2.0.1&request=GetCoverage&format=image/tiff&CoverageId=ams:abef258-08a5-4057-b154-acc4c112c678
  - JPG:
    - http://beeldbank.amsterdam.nl/component/ams_memorixbeeld_download/?view=download&format=download&id=DUIZ00345000001
  - WMS:
    - http://geoserver.memorix.nl/geoserver/ams/wms?SERVICE=WMS&REQUEST=GetMap&VERSION=1.1.1&LAYERS=ams%3Adabef258-08a5-4057-b154-acc4c112c678&STYLES=&FORMAT=image%2Fpng&TRANSPARENT=true&HEIGHT=256&WIDTH=256&ZINDEX=1&SRS=EPSG%3A4326&BBOX=4.85595703125,52.36218321674427,4.8779296875,52.3755991766591
