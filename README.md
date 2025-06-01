# Protobuf Deserializer And CLI Tool for OpenStreetMap

A lightweight command-line tool written in **C** for parsing and querying **OpenStreetMap (OSM) Protocol Buffer (PBF) files** — built entirely from scratch with **no external parsing libraries**. This project implements a protobuf deserializer that provides direct, efficient access to OSM map data in its compact binary format. The program can efficiently **deserialize** and query **any** valid PBF file.

> **Note:** This repository serves as a showcase for this project. The complete implementation is maintained in a separate private repository for academic purposes.

## Why Protocol Buffers?

**OpenStreetMap (OSM)** uses **Protocol Buffers (protobuf)** as its primary data format for handling massive geographic datasets. Protocol Buffers were developed by **Google** to efficiently serialize structured data in a language-agnostic, platform-neutral format. They use a binary encoding that's both compact and fast to parse, making them ideal for OSM's massive datasets.

See the technical details of the protobuf schema followed here: https://wiki.openstreetmap.org/wiki/PBF_Format.

## Scale and Efficiency

To illustrate the scale: the map of **Andorra**, a country of just *180 square miles*, contains nearly **500 thousand nodes**.  

In human-readable XML format, this data would be approximately **~77,000 KB**, but using Protocol Buffers' binary serialization, it's compressed to just **~3,000 KB** — a **96% reduction** by efficiently encoding all the data.

This efficiency is crucial when dealing with larger regions: continents and global datasets would be impractical to work with in XML format.

See my program deserialize and run multiple queries against Andorra's **500 thousand nodes** & **25K ways** in under a second below!

## Usage

```bash
bin/osm_parser [-h] [-f filename] [-s] [-b] [-n id] [-w id] [-w id key ...]

Options:
  -h              Help: displays this help menu
  -f filename     File: read map data from the specified file
  -s              Summary: displays map summary information
  -b              Bounding box: displays map bounding box
  -n id           Node: displays information about the specified node
  -w id           Way refs: displays node references for the specified way
  -w id key ...   Way values: displays values associated with the specified way and keys
```

## In Action

```bash
bin/osm_parser -f files/andorra.osm.pbf \
        -b \                    # Get bounding box of the map
        -s \                    # Show summary statistics
        -n 625033 \            # Look up node with ID 625033
        -n 45161231 \          # Look up node with ID 45161231
        -w 5203906 \           # Get way with ID 5203906 and its tags:
        highway \              #   - highway type
        maxspeed \             #   - speed limit
        oneway \               #   - oneway status
        ref \                  #   - reference number
        surface \              #   - surface type
        -w 6165721 \           # Get way with ID 6165721 and its tags:
        highway \              #   - highway type
        junction \             #   - junction type
        ref \                  #   - reference number
        surface                #   - surface type
```

```
=== OSM Map Query Results ===
Processing file: files/andorra.osm.pbf

=== Map Bounding Box ===
Bounding Box Coordinates:
  Minimum Longitude: 1.412360000
  Maximum Longitude: 1.787480000
  Minimum Latitude:  42.427600000
  Maximum Latitude:  42.657170000

=== Map Summary ===
Total Nodes: 484821
Total Ways: 25305

=== Node Information ===
Searching for Node ID: 625033
Node Found:
  ID: 625033
  Latitude:  42.521940000
  Longitude: 1.559610000

=== Node Information ===
Searching for Node ID: 45161231
Node Found:
  ID: 45161231
  Latitude:  42.625760000
  Longitude: 1.603870000

=== Way Key-Value Pairs ===
Way ID: 5203906
Requested Key-Value Pairs:
  highway: primary
  maxspeed: 30
  oneway: yes
  ref: N 22
  surface: asphalt

=== Way Key-Value Pairs ===
Way ID: 6165721
Requested Key-Value Pairs:
  highway: primary
  junction: roundabout
  ref: CG-2
  surface: asphalt
```

## Technical Implementation

The implementation demanded careful and precise handling of OSM’s Protocol Buffer schema, balancing efficiency, accuracy, and broad compatibility with any valid PBF file.


1. **Binary Protocol Handling**:
   - Direct parsing of varint-encoded fields and wire types
   - Multi-level nested message structures
   - String table optimization for tag management
   - Delta-encoded coordinate compression
   - Field number tracking for nested messages

2. **Data Structure Design**:
   - Custom OSM primitive implementations
   - Memory-optimized coordinate storage
   - Efficient node and way reference lookups
   - Minimal memory footprint design

The implementation strictly adheres to the OSM PBF specification, enabling reliable parsing of any valid PBF file while maintaining optimal performance and memory efficiency.

Try it out for yourself by obtaining a protobuf serialized version of an OSM Map here: https://download.geofabrik.de/

