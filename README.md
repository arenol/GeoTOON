## Preamble
This is a proposal of a new data format for the exchange of geospatial data.
As of of now, it is only at an early stage.

# GTN - GeoTOON data format.
Over the last years, we have seen several file formats for exchanging geospatial data based on existing data formats. **GML** (and **GPX**) is an implementation of **XML**, and **GeoJSON** is an implementation of **JSON**. However, a problem with these formats is that they are rather verbose; they contain a lot of unnecessary markup or repetition which makes the files unnecessary large, and not so readable for humans. Moreover, this is not optimal for transmission over Internet and local networks; geospatial data often implies large volumes of data.

There is now a new alternative available: [TOON](https://toonformat.dev/), or Token-Oriented Object Notation, is a compact and human readable file format build for LLM-driven networks. 
Token-Oriented Object Notation is a compact, human-readable encoding of the JSON data model that minimizes tokens and makes structure easy for models to follow. It's intended for LLM input as a drop-in, lossless representation of your existing JSON.

This format also lends itself well to geospatial data.
Hence, this document, aims to propose an implementation of TOON for geospatial data. 



## Example
In GeoTOON, a data file containing geospatial data, such as cities could look like this:

    cities:
      geometry: point
      dimensions: 2
      srs: "EPSG:4326"
      fields[3]{name,type,width,decimals}:
        name,string,20,0
        country,string,20,0
        population,int,10,0
      data[4]{name,country,population,geometry}:
        Tokyo,Japan,37732000,[139.6922,35.6897]
        Mexico City,Mexico,21804000,[-99.1333,19.4333]
        Mumbai,India,23973000,[72.8775,19.0761]
        São Paulo,Brasil,23086000,[-46.6333,23.5500]


## Media Type and File Extension
By convention, GeoTOON uses the file extension ```.gtn``` and the provisional media type ```text/toon``` for HTTP and content-type-aware context. Like TOON, GTN-files are always encoded in UTF-8.

## The Structure of a GTN-file
A GTN-file promises to have the following structure:

* data set name. This is a root element. A GTN-file may contain several data sets or layers.

A data set element must have the following child elements:

* **geoemtry**: Type of geometry. Must be ```point```, ```line```, or ```area```.
* **dimensions**: Number of dimensions. Must be 2 (x,y), 3(x,y,z) or 4(x,y,z,m).
* **srs**: An-EPSG code identifying the spatial reference system. If the srs is unknown, an empty string is provided.
* **fields**: Field definitions in tabular format. This  is for inserting into databases or converting to different formats, such as .shp or .gpkg
* **data**: A tabular array of the data. The geometry field must always be the last field.

## Geometry
Point geometry is always encoded as an array of elements accroding to the dimensions. This is shown in the example above

A natural way to represent *line* geometry would be to encode them as arrays of arrays, like this

    [[100,0],[200,0],[200,50],[100,50],[100,0]]
    
However, GTN-data shall use the more compact form that the TOON-format alows:

    [100,0;200,0;200,50;100,50;100,0]
    
*Area* geometry always consist of arrays of parts. This is a simple area with one part:

    [[100,0;200,0;200,50;100,50;100,0]]
    
An area with a hole is thus represented like this:

    [[100,0;200,0;200,50;100,50;100,0],[120,10;180,10:180,40;120;40;120,10]]


## Author
Agnar Renolen
agnar.renolen@gmail.com





