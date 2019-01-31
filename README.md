# PyAwReporting

An AdWords API large scale reporting tool written in Python. Reports are downloaded as plaintext files but connectivity
with a database engine such as MySQL, PostgreSQL, or MongoDB can be implemented upon request.

## Overview

[PyAwReporting](https://github.com/gmontamat/pyaw-reporting) is an open-source Python framework suitable for large scale
AdWords API reporting. Output reports are comma-separated values (CSV) plaintext files. By default, the script uses 10
threads to download reports simultaneously from different accounts. The number of threads used can be modified using the
`-n` parameter. It has been successfully tested using +100 threads making it useful for heavy load AdWords Manager
Accounts.

### Supported AdWords API version

The latest version supported by this program is
[v201809](https://ads-developers.googleblog.com/2018/09/announcing-v201809-of-adwords-api.html) with
[googleads 15.0.2](https://pypi.python.org/pypi/googleads). Older versions of the API are not supported, nor the newer
[Google Ads API Beta](https://developers.google.com/google-ads/api/docs/start).

### Important notes

This is neither an official [AdWords API](https://developers.google.com/adwords/api/) repository nor a clone of
[AwReporting](https://github.com/googleads/aw-reporting). Consider using
[AwReporting](https://github.com/googleads/aw-reporting) if you are a Java developer. This framework is both compatible
with Python 2.7 and Python 3.5+ but future releases will support Python 3.5+ only.

## Quick Start

### Prerequisites

You will need Python 2.7 or 3.5+ with the [googleads](https://pypi.python.org/pypi/googleads) package installed (using a
virtual environment is recommended). An access token *YAML* file with the corresponding AdWords credentials is also
needed. By default, the script will look for `~/googleads.yaml` unless a different path is passed. The optional
parameter **client\_customer\_id** must be included in this *YAML* file, you should enter your *AdWords Manager Account
id* (formerly known as *MCC id*). This way, all accounts linked to the Manager Account will be retrieved. The sample
file [example.yaml](awreporting/example.yaml) shows how your token should look like.

[AWQL](https://developers.google.com/adwords/api/docs/guides/awql) queries could be either passed to the script with the
`-a`/`--awql` parameter or stored in a plaintext file and passed via the `-q`/`--query` parameter. There is an sample
query in the file [query.txt](awreporting/query.txt) which will retrieve clicks and impressions per ad for the last 7
days. For more information about report types refer to
[Report Types](https://developers.google.com/adwords/api/docs/appendix/reports).

### Installation

```
$ pip install googleads
$ git clone https://github.com/gmontamat/pyaw-reporting
```

### Usage

#### Console

```
usage: awreporting.py [-h] (-a AWQL | -q QUERY_FILE) [-t TOKEN] [-o OUTPUT]
                      [-n NUM_THREAD]

PyAwReporting

optional arguments:
  -h, --help                              show this help message and exit
  -t TOKEN, --token TOKEN                 specify AdWords YAML token path
  -o OUTPUT, --output OUTPUT              define output file name
  -n NUM_THREAD, --num-thread NUM_THREAD  set number of threads

required arguments:
  -a AWQL, --awql AWQL                    pass AWQL query
  -q QUERY_FILE, --query-file QUERY_FILE  ...or use a query file
```

For example:

```
$ awreporting.py -t example.yaml -a \
  "SELECT ExternalCustomerId, CampaignId, AdGroupId, Id, Date, Clicks, Impressions \
  FROM AD_PERFORMANCE_REPORT WHERE Impressions > 0 DURING LAST_7_DAYS" \
  -o adperformance.csv -n 100
```

Or, with a query file:

```
$ awreporting.py -t example.yaml -q query.txt -o adperformance.csv -n 100
```

### About the YAML token

The example token file provided [example.yaml](awreporting/example.yaml) is not valid. Refer to
[this guide](https://developers.google.com/adwords/api/docs/guides/first-api-call) if you are using the AdWords API for
the first time.

## Useful links

* https://developers.google.com/adwords/api/
* https://github.com/googleads/googleads-python-lib
* https://github.com/googleads/aw-reporting

### Troubleshooting

We recommend to experiment the app with a small number of threads first (the default is 10) and increase the number
accordingly. The AdWords server may complain when many API calls are made at the same time but those exceptions are
handled by the app. We have successfully obtained huge reports using 200 threads.

When using this tool it might be necessary to enable a DNS cache in your system, such as
[nscd](http://man7.org/linux/man-pages/man8/nscd.8.html). This should eliminate DNS lookup problems when repeatedly
calling the AdWords API server. For example, if you find many `URLError: <urlopen error [Errno -2] Name or service
not known>` in your logs, enable the DNS cache.

In some *nix systems nscd is not enabled by default but it can be started with:

```
# systemctl start nscd
```
