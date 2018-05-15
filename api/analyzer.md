# Analyzer APIs

## Analyzer Model

- `id`: (*readonly*) the analyzer's ID once enabled within an organization
- `analyzerDefinitionId`: (*readonly*) the job's analyzer definition name
- `name`: (*readonly*) the analyzer's name
- `version`: (*readonly*) the analyzer's version
- `description`: (*readonly*) the analyzer's decription
- `author`: (*readonly*) the analyzer's author
- `url`: (*readonly*) the analyzer's url
- `license`: (*readonly*) the analyzer's license
- `dataTypeList`: (*readonly*) the analyzers' allowed data types
- `baseConfig`: (*readonly*) the analyzer's base configuration name. This will be used to identify the shared set of configuration with all analyzer flavors.
- `jobCache`: the analyzer's cache timeout in minutes
- `configuration`: (*writable*) the analyer's configuration. A JSON object where key/value pairs reptesent the config names, and their values. It includes the default properties `proxy_http`, `proxy_https`, `auto_extract_artifacts`, `check_tlp`, `max_tlp`
- `createdAt`: (*generated*) the creation date
- `createdBy`: (*generated*) the user who enabled the analyzer
- `updatedAt`: (*generated*) the last update date
- `updatedBy`: (*generated*) the latest user who updated the analyzer

## Enable  API (**Required Roles**: `orgadmin`)

POST /api/organization/analyzer/:analyzerId

## List API (**Required Roles**: `analyze`, `orgadmin`)

This API allows a user to list and search all the enabled analyzers withing the organization

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer'
```

or

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer/_search' -d '{
  query: {}
}'
```

Both APIs supports the `range` and `sort` query params declared in [paging and sorting details](misc.md#paging-and-sorting), and both return a JSON array of analyzer objects as described in [Analyzer Model section](#analyzer-model).

If called by a user other than an `orgadmin`, the `configuration` attribute is not included on the JSON objects.

## Get analyzer API (**Required Roles**: `analyze`, `orgadmin`)

This API allows a user to get an analyzer details.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer/ANALYZER_ID'
```

It returns a analyzer JSON object as described in [Analyzer Model section](#analyzer-model)

If called by a user other than an `orgadmin`, the `configuration` attribute is not included on the JSON objects.

## Get by type API (**Required Roles**: `analyze`, `orgadmin`)

This API is mostly used by TheHive and allows to quickly get the list of analyzers that can run on the given data type

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer/type/DATA_TYPE'
```

It returns a JSON array of analyzer objects as described in [Analyzer Model section](#analyzer-model) without the `configuration` attribute, which could contain sensitive data.

## Update API (**Required Roles**: `orgadmin`)

This API allows an `orgadmin` user to update the `name`, `configuration` and `jobCache` of an enabled analyzer.

```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer/ANALYZER_ID' -d '{
  "configuration": {
    "key": "XXXXXXXXXXXXXXXXXXXXXXXXXXXx",
    "polling_interval": 60,
    "proxy_http": "http://localhost:8080",
    "proxy_https": "http://localhost:8080",
    "auto_extract_artifacts": true,
    "check_tlp": true,
    "max_tlp": 1
  },
  "name": "Shodan_Host_1_0",
  "jobCache": null
}'
```

It returns a JSON object describing the analyzer as defined in [Analyzer Model section](#analyzer-model)

## Run API (**Required Roles**: `analyze`, `orgadmin`)

This API allows running analyzers on observables of different data types.

For `files`, the API call must be made as described below:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer/ANALYZER_ID/run' \
  -F 'attachment=@/path/to/observable-file' \
  -F '_json=<-;type=application/json' << _EOF_
  {
    "dataType":"file",
    "tlp":0
  }
_EOF_
```

for other observable types the request is:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer/ANALYZER_ID/run' -d '{
  "data":"8.8.8.8",
  "dataType":"ip",
  "tlp":0,
  "message": "A message that can be accessed from the analyzer",
  "parameters": {
    "key1": "value1",
    "key2": "value2"
  }
}'
```

This call will fetch a similar job from the cache, and if it finds one, it returns it from the cache, based on the duration defined in `jobCache` attribute of the analyzer.

To force bypassing the cache, one can add the following query param: `force=1`

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer/ANALYZER_ID/run?force=1' -d '{
  "data":"8.8.8.8",
  "dataType":"ip",
  "tlp":0,
  "message": "A message that can be accessed from the analyzer",
  "parameters": {
    "key1": "value1",
    "key2": "value2"
  }
}'
```

## Delete API (**Required Roles**: `orgadmin`)

This API allows an `orgadmin` to disable and delete an existing analyzer instance.

```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/analyzer/ANALYZER_ID'
```