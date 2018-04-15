# plist-utils


### `plprint`

Convert a file in Apple's "property list format" into XML (whether binary or already XML),
then pretty print with `xmllint`.
The plist file can be supplied as a single argument, or on `/dev/stdin`.

    plprint Config.plist


### `pljson`

Simplify the XML of the given plist file with XMLStarlet,
replacing `<data>` and `<date>` elements with `<string>` elements,
and convert to JSON.

    pljson Config.plist


## Recipes

The macOS `airport` utility has a scan option, `-s`,
but by default, the output is unstructured and difficult to manipulate.
The `-x` option produces XML that contains a _lot_ more information in structured (if verbose) `plist` format.

Using `pljson` and a few other commands,
we can sort the available networks with strongest (largest RSSI) connections first:

      airport -s -x \
      | iconv -f macroman -t UTF-8 \
      | pljson \
      | jq -c '.[] | {SSID_STR,BSSID,RSSI,NOISE,CHANNEL,CHANNEL_FLAGS,CAPABILITIES}' \
      | mlr --ijson --opprint sort -nr RSSI

Pipeline components:

1. `airport -s -x`:
   Run a network scan and produce XML output.
2. `iconv`:
   Convert encoding from Mac-Roman to UTF-8.
   Sometimes networks advertise names that are not UTF-8, which `airport` doesn't convert,
   despite clearly denoting the encoding as `UTF-8` in the XML declaration.
3. [`pljson`](#pljson):
   Convert property list format to JSON.
4. [`jq`](https://stedolan.github.io/jq/):
   Flatten the top-level array and select just a few fields from each network dictionary.
5. [`mlr`](https://johnkerl.org/miller/doc/):
   Read in JSON and output whitespace-aligned tabular output, sorting by descending RSSI.
   RSSI ranges from -∞ (or maybe -100) to 0.

   | RSSI | Description       |
   |-----:|:------------------|
   |    0 | impossibly strong |
   |  -30 | amazing           |
   |  -70 | average           |
   |  -90 | unusable          |


## License

Copyright 2017-2018 Christopher Brown.
[MIT Licensed](https://chbrown.github.io/licenses/MIT/#2017-2018).
