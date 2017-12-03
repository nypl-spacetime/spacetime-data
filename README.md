# NYC Space/Time Directory - Data & Tools

This repository lists resources useful when working with [NYC Space/Time Directory](http://spacetime.nypl.org/) data.

## Datasets

See http://spacetime.nypl.org/#data

## Data Model & JSON Schemas

See https://github.com/nypl-spacetime/spacetime-schemas

## Tools

- Command line tools: https://github.com/nypl-spacetime/spacetime-cli

## Examples

The examples below use the following tools:

- [jq](https://stedolan.github.io/jq/): command-line JSON processor, install with `brew install jq`
- [ndjson-cli](https://github.com/mbostock/ndjson-cli): command-line tools for operating on newline-delimited JSON streams, install with `npm install -g ndjson-cli`
- [spacetime-cli](https://github.com/nypl-spacetime/spacetime-cli): command-line tools for NYC Space/Time Directory data, install with `npm install -g nypl-spacetime/spacetime-cli`

### Convert Data to JSON

Download [Map Warper](http://maps.nypl.org/warper) data from the NYC Space/Time Directory and convert to a JSON array:

    curl http://s3.amazonaws.com/spacetime-nypl-org/\
    datasets/mapwarper/mapwarper.objects.ndjson \
    | spacetime-to-json

Use jq to only display the ID and the area the map depicts:

    curl http://s3.amazonaws.com/spacetime-nypl-org/\
    datasets/mapwarper/mapwarper.objects.ndjson \
    | spacetime-to-json | \
    jq '.[] | {id: .id, area: .data.area}'

Use ndjson-filter to filter Building Inspector data by year, and save the resulting GeoJSON file to disk:

    curl http://s3.amazonaws.com/spacetime-nypl-org/\
    datasets/building-inspector/building-inspector.objects.ndjson \
    | ndjson-filter 'd.validSince > 1880' | spacetime-to-geojson > \
    ~/Downloads/building-inspector-1880.geojson

Use ndjson-map to only display the ID and the name, and then grep for *Harlem*:

    curl http://s3.amazonaws.com/spacetime-nypl-org/\
    datasets/mapwarper/mapwarper.objects.ndjson \
    | ndjson-map '`${d.id} - ${d.name}`' | grep Harlem

### Convert data to GeoJSON

Download Map Warper data, filter maps which title includes *broadway*, convert to GeoJSON and copy to clipboard:

    curl http://s3.amazonaws.com/spacetime-nypl-org/\
    datasets/mapwarper/mapwarper.objects.ndjson \
    | ndjson-filter 'd.name && d.name.toLowerCase().includes("broadway")' \
    | spacetime-to-geojson | pbcopy

Then, we can paste this GeoJSON data into [geojson.io](http://geojson.io/):

![](geojson.io.png)
