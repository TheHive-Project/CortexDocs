# Job APIs

## Job Model

- `id`: (*generated*) the job's generated ID
- `organization`: (*readonly*) the job's organization
- `analyzerDefinitionId`: (*readonly*) the job's analyzer full name
- `analyzerId`: (*readonly*) the job's analyzer's instance ID
- `analyzerName`: (*readonly*) the job's analyzer's name
- `dataType`: (*readonly*) the job's analyzed observable type
- `status`: (*generated*) the job's status(`Waiting`, `InProgress`, `Success`, `Failure`, `Deleted`)
- `data`: (*readonly*) available only for non `file` observables, represents the job's observable's value
- `attachment`: (*readonly*) available only for `file` observables, a JSON object that defines the file `name`, `hashes`, `size`, `contentType` and `id`
- `parameters`: (*readonly*) set during creation, this is a JSON object of key/value pairs
- `message`: (*readonly*) a free text field to set additional text/context to a job
- `tlp`: (*readonly*) the TLP of the analyzed observable
- `startDate`: (*generated*) the job start date
- `endDate`: (*generated*) the job end date
- `createdAt`: (*generated*) the creation date
- `createdBy`:  (*generated*) the user who started the job
- `updatedAt`:  (*generated*) the last update date
- `updatedBy`:  (*generated*) the latest user who updated the job

## List API (**Required Roles**: `read`, `analyze`, `orgadmin`)

This API allows a user with `read` or `analyzer` role to list and search all the analysis jobs made by its organization:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/job/_search' -d '{
  query: {
    "_and": [
      {"status": "Success"},
      {"dataType": "ip"}
    ]
  }
}'
```

It supports the `range` and `sort` query params declared in [paging and sorting details](misc.md#paging-and-sorting)

## Get API (**Required Roles**: `read`, `analyze`, `orgadmin`)

This API allows a user to get the details of a job, without fetching the job's report.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/job/JOB_ID'
```

It returns a JSON response with the following structure:

```json
{
  "id": "AWNei4vH3rJ8unegCPB9",
  "analyzerDefinitionId": "Abuse_Finder_2_0",
  "analyzerId": "220483fde9608c580fb6a2508ff3d2d3",
  "analyzerName": "Abuse_Finder_2_0",
  "status": "Success",
  "data": "8.8.8.8",
  "parameters": "{}",
  "tlp": 0,
  "message": "",
  "dataType": "ip",
  "organization": "demo",
  "startDate": 1526299593923,
  "endDate": 1526299597064
  "date": 1526299593633,
  "createdAt": 1526299593633,
  "createdBy": "demo",
  "updatedAt": 1526299597066,
  "updatedBy": "demo"
}
```

## Get report API (**Required Roles**: `read`, `analyze`, `orgadmin`)

This API allows a user to get the details of a job including the job's report.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/job/JOB_ID/report'
```

It returns a JSON response with the following structure:

```json
{
  "id": "AWNei4vH3rJ8unegCPB9",
  "analyzerDefinitionId": "Abuse_Finder_2_0",
  "analyzerId": "220483fde9608c580fb6a2508ff3d2d3",
  "analyzerName": "Abuse_Finder_2_0",
  "status": "Success",
  "data": "8.8.8.8",
  "parameters": "{}",
  "tlp": 0,
  "message": "",
  "dataType": "ip",
  "organization": "demo",
  "startDate": 1526299593923,
  "endDate": 1526299597064,
  "date": 1526299593633,
  "createdAt": 1526299593633,
  "createdBy": "demo",
  "updatedAt": 1526299597066,
  "updatedBy": "demo",
  "report": {
    "summary": {
      "taxonomies": [
        {
          "predicate": "Address",
          "namespace": "Abuse_Finder",
          "value": "network-abuse@google.com",
          "level": "info"
        }
      ]
    },
    "full": {
      "abuse_finder": {
        "raw": "...",
        "abuse": [
          "network-abuse@google.com"
        ],
        "names": [
          "Google LLC",
          "Level 3 Parent, LLC"
        ],
        "value": "8.8.8.8"
      }
    },
    "success": true,
    "artifacts": []
  }
}
```

## Delete API (**Required Roles**: `analyze`, `orgadmin`)

This API allows a user to delete a job:

```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/job/JOB_ID'
```

This marks the job as `Deleted` and doesn't remove the job's data from the database.