# plist-utils

* `plprint`

  Convert a file in Apple's "property list format" into XML (whether binary or already XML),
  then pretty print with `xmllint`.
  The plist file can be supplied as a single argument, or on `/dev/stdin`.

      plprint Config.plist

* `pljson`

  Simplify the XML of the given plist file with XMLStarlet,
  replacing <data> and <date> elements with <string> elements,
  and convert to JSON.

      pljson Config.plist

  Full example, using output from the macOS `airport` utility, strongest connections first:

      airport -s -x | pljson | jq -c '.[] | {SSID_STR,BSSID,RSSI,NOISE,CHANNEL,CHANNEL_FLAGS,AP_MODE,CAPABILITIES}' | mlr --ijson --opprint sort -nr RSSI


## License

Copyright 2017-2018 Christopher Brown.
[MIT Licensed](https://chbrown.github.io/licenses/MIT/#2017-2018).
