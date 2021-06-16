# OpenStreetMapSpeeds Schema

This repository houses both a description/outline of the JSON schema for the data artifact the [conflation](https://github.com/OpenStreetMapSpeeds/conflation) repository produces as well as the latest version of the artifact itself.

## Schema

Broadly the schema is a collection of nested JSON objects. At the top level we have an array of objects where each object represents a geographic region. The region may be specified using the optional [iso3166-1 country](https://en.wikipedia.org/wiki/ISO_3166-1) and [iso3166-2 principal subdivision](https://en.wikipedia.org/wiki/ISO_3166-2) codes. These geographies are treated as a hierarchy to allow varying degrees of specificity. For example one can specify a global set of speeds (i.e. applies to the whole world) by simply omitting both the `iso3166-1` and `iso3166-2` keys in a given object. To specify speeds across an entire country one needs to only populate the `iso3166-1` key and omit the `iso3166-2` key. For full granularity one must include both `iso3166-1` and `iso3166-2`.

Within a given object there are 3 divisions based on assumed changes in road network congestion, these are `rural`, `suburban` and `urban`. We deliberately avoid a one size fits all defintion of these categorizations as they may differ from system to system. See the [conflation](https://github.com/OpenStreetMapSpeeds/conflation) repo for more information about where the data comes from and how it is used to populate these divisions.

Within each density/populational division we further classify by road types which should be somewhat familiar to those familiar with OSM. There are really only two types of information we use to do this classification, the functional road class (FRC or `highway` tag in OSM) and the form of way (FoW may come from many different tag values in OSM). The `highway` tag values that we assign speeds for are in descending importance: `motorway`, `trunk`, `primary`, `secondary`, `tertiary`, `unclassified`, `residential` and `service`.

We'll start by describing those values who only rely on their form of way, ie `driveway`, `alley`, `parking_aisle` and `drive-through`. These are all tagged in OSM as `highway=service` and then `service=*` where `*` is one of `driveway`, `alley`, `parking_aisle` or `drive-through`. Because they have only one value for their `highway` tag they only apply to that 1 functional road class (the lowest, i.e. `service`) and so they have only a single value for speed.

We also differentiate roundabouts which are tagged as `junction=roundabout` on OSM ways. Since they may occur with any `highway` tag from above we have an array for their speeds where the first speed in the array corresponds to the `motorway` speed and the last speed in the `roundabout` array corresponds to the `service` speed.

For the `highway=*_link` tags we have two classifications. Those that appear with any kind of signage (eg. `destination=*`) which we call `link_exiting` or those without signage which we call `link_turning`. Note that we only support an array of speeds for the 5 most important `highway=*_link` tags ie. `motorway_link`, `trunk_link`, `primary_link`, `secondary_link` and `tertiary_link`.

Finally we the `ways` array, which is for ways which do not have a designated or implied form of way. For these we simply supply a speed per `highway` tag as mentioned above, where the first speed is for `highway=motorway` and the last speed is for `highway=service`.

The schema, since it uses arrays, does not allow for the omission of values however one could use `0` or `null` to signal no data for a particular categorization.

## Sample Data

Below is a visual sample of data schema. For the latest actual data please refer to [default_speeds.json](default_speeds.json).

```json
[
  {
    "rural": {
      "way": [100,65,55,45,35,25,20,10],
      "link_exiting": [50,45,40,40,40],
      "link_turning": [50,35,35,30,30],
      "roundabout": [50,35,25,25,25,20,20,10],
      "driveway": 15,"alley": 10,"parking_aisle": 15,"drive-through": 10
    },
    "suburban": {
      "way": [90,50,40,35,30,20,15,10],
      "link_exiting": [60,45,40,40,35],
      "link_turning": [55,35,30,25,25],
      "roundabout": [30,30,25,20,20,20,20,15],
      "driveway": 15,"alley": 10,"parking_aisle": 10,"drive-through": 10
    },
    "urban": {
      "way": [80,35,30,30,25,20,15,10],
      "link_exiting": [60,40,35,35,30],
      "link_turning": [60,30,25,20,20],
      "roundabout": [25,25,20,20,20,20,15,10],
      "driveway": 15,"alley": 10,"parking_aisle": 10,"drive-through": 10
    }
  },
  {
    "iso3166-1": "US",
    "rural": {
      "way": [105,90,55,45,40,30,20,10],
      "link_exiting": [50,50,40,40,35],
      "link_turning": [45,45,35,35,30],
      "roundabout": [25,30,25,25,25,20,20,15],
      "driveway": 15,"alley": 10,"parking_aisle": 15,"drive-through": 15
    },
    "suburban": {
      "way": [90,80,35,30,30,25,20,10],
      "link_exiting": [55,50,40,35,30],
      "link_turning": [50,50,35,30,25],
      "roundabout": [30,30,25,20,20,20,20,15],
      "driveway": 15,"alley": 10,"parking_aisle": 15,"drive-through": 10
    },
    "urban": {
      "way": [70,55,20,20,20,20,15,10],
      "link_exiting": [50,45,35,25,30],
      "link_turning": [50,40,25,20,20],
      "roundabout": [20,20,20,20,20,15,15,10],
      "driveway": 15,"alley": 10,"parking_aisle": 15,"drive-through": 10
    }
  },
  {
    "iso3166-1": "CH",
    "iso3166-2": "AI",
    "rural": {
      "way": [90,45,35,30,25,20,15,10],
      "link_exiting": [45,40,40,40,30],
      "link_turning": [35,20,20,20,15],
      "roundabout": [30,30,25,25,25,25,15,10],
      "driveway": 15,"alley": 15,"parking_aisle": 10,"drive-through": 10
    },
    "suburban": {
      "way": [80,35,30,30,25,20,15,10],
      "link_exiting": [40,30,25,30,25],
      "link_turning": [35,25,15,20,15],
      "roundabout": [20,20,20,20,20,20,15,15],
      "driveway": 10,"alley": 15,"parking_aisle": 10,"drive-through": 10
    },
    "urban": {
      "way": [60,30,25,20,20,20,15,10],
      "link_exiting": [30,20,20,20,20],
      "link_turning": [30,15,15,15,15],
      "roundabout": [20,20,15,20,20,15,15,10],
      "driveway": 15,"alley": 15,"parking_aisle": 10,"drive-through": 10
    }
  }
]
```

## Data Sources and Intended Use

At the moment the data sources used to generate the [speed data](default_speeds.json) are [Mapillary](https://mapillary.com) for GPS traces and [OSM](https://osm.org) to which the traces are matched (via the [Valhalla](https://github.com/valhalla/valhalla routing APIs)). These are the only two sources that are currently supported in the [conflation](https://github.com/OpenStreetMapSpeeds/conflation) tool which is tasked with generating this data automatically. Mapillary has been a huge supporter of OpenStreetMap since it's inception and allows its APIs to be used to improve OSM (see section 13 of their [terms](https://www.mapillary.com/terms)). This allows us to generate "default" speed values for different parts of the world and make that information readily available along side and for use with the OSM dataset. Since adding the data directly to OSM would likely be considered vandalism (but also a huge waste of space) we will instead opt to link this data artifact either on an existing OSM wiki page (eg. https://wiki.openstreetmap.org/wiki/Average_speed_per_way, https://wiki.openstreetmap.org/wiki/Proposed_features/Practical_maxspeed) or create a new one and advertize it to folks looking for sensible defaults. One example of a place these defaults could be useful would be in any OSM-based router, and indeed the Valhalla router currently supports this data format for specifying default motor vehicle speeds in its graph creation.

Thus far, the data currently available in this repository has been manually generated. As such, the data is coarse both in terms of the indivual speeds (5kph increments) but also in terms of their geographic specificity (global and a few countries exist). The primary tools used in generating these values by-hand were Mapillary and its APIs as well as [Overpass Turbo](https://overpass-turbo.eu/) (which is a query API on top of OSM). The steps are basically to use Overpass Turbo to find ways that match the criteria of the part of the schema you are trying to fill in the geography you are trying to fill it. Once you've found some ways you need to find where they are covered in Mapillary's map. Once you've done that you can use the Mapillary API's to get the timestamps when a person got on and off of the way you are interested in. Finally you need to get the length of the way, which you can do with a router either directly on osm.org or with whatever OSM-based router you have access to. To speed up the process, especially for those types of ways who have multiple highway tags, one can just get estimates of the motorway, trunk and service speeds and do a linear interpolation for the rest of the speeds between trunk and service.

Long term, the [conflation](https://github.com/OpenStreetMapSpeeds/conflation) tool will generate the data in the above described format. It will do so in a manner that is not dissimilar to the manual process. In short, the tool will pull GPS trace data from Mapillary's APIs, use Valhalla to map-match those traces to OSM, use the match metadata to aggregate the observations of ways that share the same properties and finally build the data artifact from those aggregations. The hope is that by averaging out many many observations we can arrive at a least squares fit per geography, per density, per road type. So far even the manual tuning has shown positive results.
