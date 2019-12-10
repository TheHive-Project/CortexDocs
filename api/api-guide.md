# API Guide
This guide applies only to Cortex 2 and newer. It is not applicable to Cortex 1.

## Table of Contents
  * [Introduction](#introduction)
    * [Request &amp; Response Formats](#request--response-formats)
    * [Authentication](#authentication)
  * [Organization APIs](#organization-apis)
    * [Organization Model](#organization-model)
    * [List](#list)
    * [Create](#create)
    * [Update](#update)
    * [Delete](#delete)
    * [Obtain Details](#obtain-details)
    * [List Users](#list-users)
    * [List Enabled Analyzers](#list-enabled-analyzers)
  * [User APIs](#user-apis)
    * [User Model](#user-model)
    * [List All](#list-all)
    * [List Users within an Organization](#list-users-within-an-organization)
    * [Search](#search)
    * [Create](#create-1)
    * [Update](#update-1)
    * [Get Details](#get-details)
    * [Set a Password](#set-a-password)
    * [Change a password](#change-a-password)
    * [Set and Renew an API Key](#set-and-renew-an-api-key)
    * [Get an API Key](#get-an-api-key)
    * [Revoke an API Key](#revoke-an-api-key)
  * [Job APIs](#job-apis)
    * [Job Model](#job-model)
    * [List and Search](#list-and-search)
    * [Get Details](#get-details-1)
    * [Get Details and Report](#get-details-and-report)
    * [Wait and Get Job Report](#wait-and-get-job-report)
    * [Get Artifacts](#get-artifacts)
    * [Delete](#delete-1)
  * [Analyzer APIs](#analyzer-apis)
    * [Analyzer Model](#analyzer-model)
    * [Enable](#enable)
    * [List and Search](#list-and-search-1)
    * [Get Details](#get-details-2)
    * [Get By Type](#get-by-type)
    * [Update](#update-2)
    * [Run](#run)
    * [Disable](#disable)
  * [Miscellaneous APIs](#miscellaneous-apis)
    * [Paging and Sorting](#paging-and-sorting)


## Introduction
Cortex 2 offers a REST API that can be leveraged by various applications and programs to interact with it. The following guide describe the Cortex 2 API to allow developers to interface the powerful observable analysis engine with other SIRPs (Security Incident Response Platforms) besides TheHive, TIPs (Threat Intelligence Platforms), SIEMs or scripts. Please note that the Web UI of Cortex 2 exclusively leverage the REST API to interact with the back-end.

**Note**: You can use [Cortex4py](https://github.com/TheHive-Project/Cortex4py), the Python library we provide, to facilitate interaction with the REST API of Cortex. You need Cortex4py 2.0.0 or later as earlier versions are not compatible with Cortex 2.

All the exposed APIs share the same [request & response formats](#request--response-formats) and [authentication strategies](#authentication) as described below.

There are also some transverse parameters supported by several calls, in addition to [utility APIs](#miscellaneous-apis).

If you want to create an analyzer, please read the [How to Write and Submit an Analyzer ](how-to-create-an-analyzer.md) guide.

### Request & Response Formats
Cortex accepts several parameter formats within a HTTP request. They can be used indifferently. Input data can be:

- A query string
- A URL-encoded form
- A multi-part
- JSON

Hence, the requests shown below are equivalent.

#### Query String
```bash
curl -XPOST 'https://CORTEX_APP_URL:9001/api/login?user=me&password=secret'
```

#### URL-encoded Form
```bash
curl -XPOST 'https://CORTEX_APP_URL:9001/api/login' -d user=me -d password=secret
```

#### JSON
```bash
curl -XPOST https://CORTEX_APP_URL:9001/api/login -H 'Content-Type: application/json' -d '{
  "user": "me",
  "password": "secret"
}'
```

#### Multi-part
```bash
curl -XPOST https://CORTEX_APP_URL:9001/api/login -F '_json=<-;type=application/json' << _EOF_
{
  "user": "me",
  "password": "secret"
}
_EOF_
```

#### Response Format
For each request submitted, Cortex will respond back with JSON data. For example, if the authentication request is successful, Cortex should return the following output:

```json
{"id":"me","name":"me","roles":["read","analyze","orgadmin"]}
```

If not, Cortex should return an authentication error:

```json
{"type":"AuthenticationError","message":"Authentication failure"}
```

### Authentication
Most API calls require authentication. Credentials can be provided using a **session cookie**, an **API key** or directly using HTTP **basic
authentication** (if this method is specifically enabled).

Session cookies are better suited for browser authentication. Hence, **we recommend authenticating with API keys** when calling the Cortex APIs.

#### Generating API Keys with an orgAdmin Account
API keys can be generated using the Web UI. To do so, connect using an `orgAdmin` account then click on *Organization* and then on the `Create API Key` button in the row corresponding to the user you intend to use for API authentication. Once the API key has been created, click on `Reveal` to display the API key then click on the *copy to clipboard* button if you wish to copy the key to your system's clipboard.

If the user is not yet created, start by clicking on `Add user` to create it then follow the steps mentioned above.

#### Generating API Keys with a superAdmin Account
You can use a `superAdmin` account to achieve the same result as described above. Once authenticated, click on *Users* then on the `Create API Key` button in the row corresponding to the user you intend to use for API authentication. Please **make sure the user is in the right organization** by thoroughly reading its name, which is shown below the user name. Once the API key has been created, click on `Reveal` to display the API key then click on the *copy to clipboard* button if you wish to copy the key to your system's clipboard.

#### Authenticating with an API Key
Once you have generated an API key you can use it, for example, to list the Cortex jobs thanks to the following `curl` command:

```bash
# Using API key
curl -H 'Authorization: Bearer **API_KEY**' https://CORTEX_APP_URL:9001/api/job
```
As you can see in the example above, we instructed `curl` to add the *Authorization* header to the request. The value of the header is `Bearer: **API_KEY**`. So if your API key is `GPX20GUAQWwpqnhA6JpOwNGPMfWuxsX3`, the `curl` command above would look like the following:

```bash
# Using API key
curl -H 'Authorization: Bearer GPX20GUAQWwpqnhA6JpOwNGPMfWuxsX3' https://CORTEX_APP_URL:9001/api/job
```

#### Using Basic Authentication
Cortex also supports basic authentication but it is disabled by default for security reasons. **If you absolutely need to use it**, you can enable it by adding `auth.method.basic=true` to the configuration file (`/etc/cortex/application.conf` by default). Once you do, restart the Cortex service. You can then, for example, list the Cortex jobs using the following `curl` command:

```bash
# Using basic authentication
curl -u mylogin:mypassword https://CORTEX_APP_URL:9001/api/job
```

## Organization APIs
Cortex offers a set of APIs to create, update and list organizations.

### Organization Model
An organization (org) is defined by the following attributes:

| Attribute | Description | Type |
| --------- | ----------- | ---- |
|`id` | Copy of the org's name (see next row) | readonly |
|`name` | Name | readonly |
|`status` | Status (`Active` or `Locked`) | writable |
|`description` | Description | writable |
|`createdAt` | Creation date | computed |
|`createdBy` | User who created the org | computed |
|`updatedAt` | Last update | computed |
|`updatedBy` | User who last updated the org | computed |

Please note that `id` and `name` are essentially the same. Also, `createdAt` and `updatedAt` are in *epoch*.

### List
It is possible to list all the organizations using the following API call, which requires the API key associated with a `superAdmin` account:

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/organization'
```

You can also search/filter organizations using the following query:

```bash
curl -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/organization/_search' -d '{
  "query": {"status": "Active"}
}'
```

Both APIs supports the `range` and `sort` query parameters described in [paging and sorting details](#paging-and-sorting).

### Create
It is possible to create an organization using the following API call, which requires the API key associated with a `superAdmin` account:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/organization' -d '{
  "name": "demo",
  "description": "Demo organization",
  "status": "Active"
}'
```

### Update
You can update an organization's description and status (`Active` or `Locked`) using the following API call. This requires the API key associated with a `superAdmin` account:

```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/organization/ORG_ID' -d '{
  "description": "New Demo organization",
}'
```

or

```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/organization/ORG_ID' -d '{
  "status": "Active",
}'
```

### Delete
Deleting an organization just marks it as `Locked` and doesn't remove the associated data from the DB. To "delete" an organization, you can use the API call shown below. It requires the API key associated with a `superAdmin` account.

```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/organization/ORG_ID'
```

### Obtain Details
This API call returns the details of an organization as described in the [Organization model](#organization-model) section.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/organization/ORG_ID'
```

Let's assume that the organization we are seeking to obtain details about is called *demo*. The `curl` command would be:

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/organization/demo'
```

and it should return:

```json
{
  "id": "demo",
  "name": "demo",
  "status": "Active",
  "description": "Demo organization",
  "createdAt": 1520258040437,
  "createdBy": "superadmin",
  "updatedBy": "superadmin",
  "updatedAt": 1522077420693
}
```

### List Users
As mentioned above, you can use the API to return the list of **all** the users declared withing an organization. For that purpose, use the API call shown below with the API key of an `orgAdmin` or `superAdmin` account. It supports the `range` and `sort` query parameters declared in [paging and sorting details](#paging-and-sorting).

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/organization/ORG_ID/user'
```

and should return a list of [users](#user-model).

If one wants to filter/search for some users (active ones for example), there is a search API to use as below:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/organization/ORG_ID/user/_search' -d '{
  "query": {}
}'
```

It also supports the `range` and `sort` query parameters declared in [paging and sorting details](#paging-and-sorting).

### List Enabled Analyzers
To list the analyzers that have been enabled within an organization, use the following API call with the API key of an `orgAdmin` user:

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/analyzer'
```

It should return a list of [Analyzers](#analyzer-model).

Please note that this API call does not display analyzers that are disabled. It supports the `range` and `sort` query parameters declared in [paging and sorting details](#paging-and-sorting).

## User APIs
The following section describes the APIs that allow creating, updating and listing users within an organization.

### User Model
A user is defined by the following attributes:

| Attribute | Description | Type |
| --------- | ----------- | ---- |
|`id` | ID/login | readonly |
|`name` | Name | writable |
|`roles`| Roles. Possible values are: `read`, `read,analyze`, `read,analyze,orgadmin` and `superadmin` | writable |
|`status` | Status (`Active` or `Locked`) | writable |
|`organization` | organization to which the user belongs (set upon account creation) | readonly |
|`createdAt` | Creation date | computed |
|`createdBy` | User who created the account | computed |
|`updatedAt` | Last update date | computed |
|`updatedBy` | User who last updated the account| computed |
|`hasKey` | true when the user has an API key | computed |
|`hasPassword` | true if the user has a password | computed |

### List All
This API call allows a `superAdmin` to list and search all the users of all defined organizations:

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/user'
```

This call supports the `range` and `sort` query parameters declared in [paging and sorting details](#paging-and-sorting).

### List Users within an Organization
This call is described in [organization APIs](#organization-apis).

### Search
This API call allows a `superAdmin` to perform search on the user accounts created in a Cortex instance:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/user/_search' -d '{
  "query": {}
}'
```

This call supports the `range` and `sort` query parameters declared in [paging and sorting details](#paging-and-sorting)

### Create
This API calls allows you to programmatically create user creation. If the call is made by a `superAdmin` user, the request must specify the organization to which the user belong in the `organization` field.

If the call is made by an `orgAdmin` user, the value of `organization` field must be the same as the user who makes the call: `orgAdmin` users are allowed to create users only in their organization.

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/user' -d '{
  "name": "Demo org Admin",
  "roles": [
    "read",
    "analyze",
    "orgadmin"
  ],
  "organization": "demo",
  "login": "demo"
}'
```

If successful, the call returns a JSON object representing the created user as described [above](#user-model).

```json
{
  "id": "demo",
  "organization": "demo",
  "name": "Demo org Admin",
  "roles": [
    "read",
    "analyze",
    "orgadmin"
  ],
  "status": "Ok",
  "createdAt": 1526050123286,
  "createdBy": "superadmin",
  "hasKey": false,
  "hasPassword": false
}
```

### Update
This API call allows updating the writable attributed of a user account. It's available to users with `superAdmin` or `orgAdmin` roles. Any user can also use it to update their own information (but obviously not their roles).

```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/user/USER_LOGIN' -d '{
  "name": "John Doe",
  "roles": [
    "read",
    "analyze"
  ],
  "status": "Locked"
}'
```

It returns a JSON object representing the updated user as described [above](#user-model).

### Get Details
This call returns the user details. It's available to users with `superAdmin` roles and to users in the same organization. Every user can also use it to read their own details.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/user/USER_LOGIN'
```
It returns a JSON object representing the user as described [previously](#user-model).

### Set a Password
This call sets the user's password. It's available to users with `superAdmin` or `orgAdmin` roles. Please note that the request needs to be made using HTTPS with a valid certificate on the server's end to prevent credential sniffing or other PITM (Person-In-The-Middle) attacks.

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/user/USER_LOGIN/password/set' -d '{
  "password": "SOMEPASSWORD"
}'
```

If successful, the call returns 204 (success / no content).

### Change a password
This call allows a given user to change only **their own** existing password. It is available to all users including `superAdmin` and `orgAdmin` ones. Please note that if a `superAdmin` or an `orgAdmin` needs to update the password of another user, they must use the `/password/set` call described in the previous subsection.

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/user/USER_LOGIN/password/change' -d '{
  "currentPassword": "password",
  "password": "new-password"
}'
```

If successful, the call returns 204 (success / no content).

### Set and Renew an API Key
This calls allows setting and renewing the API key of a user. It's available to users with `superAdmin` or `orgAdmin` roles. Any user can also use it to renew their own API key. Again, the request needs to be made using HTTPS with a valid certificate on the server's end to prevent credential sniffing or other PITM (Person-In-The-Middle) attacks. You know the drill ;-)

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/user/USER_LOGIN/key/renew'
```

If successful, it returns the generated API key in a `text/plain`response.

### Get an API Key
This calls allows getting a user's API key. It's available to users with `superAdmin` or `orgAdmin` roles. Any user can also use it to obtain their own API key.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/user/USER_LOGIN/key'
```

If successful, the generated API key is returned in `text/plain`response

### Revoke an API Key
This calls allow revoking a user's API key. This calls allow revoking a user's API key.

```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/user/USER_LOGIN/key'
```

A successful request returns nothing (HTTP 200 OK).

## Job APIs
The following section describes the APIs that allow manipulating jobs. Jobs are basically submissions made to analyzers and the resulting reports.

### Job Model
A job is defined by the following attributes:

| Attribute | Description | Type |
| --------- | ----------- | ---- |
| `id` | Job ID | computed |
| `organization` | The organization to which the job belongs | readonly |
| `analyzerDefinitionId`| Analyzer definition name | readonly |
| `analyzerId` | Instance ID of the analyzer to which the job is associated  | readonly |
| `organization` | Organization to which the user belongs (set upon account creation) | readonly |
| `analyzerName` | Name of the analyzer to which the job is associated | readonly |
| `dataType` | the datatype of the analyzed observable | readonly |
| `status` | Status of the job (`Waiting`, `InProgress`, `Success`, `Failure`, `Deleted`) | computed |
| `data` | Value of the analyzed observable (does not apply to `file` observables) | readonly |
| `attachment` | JSON object representing `file` observables (does not apply to non-`file` observables). It  defines the`name`, `hashes`, `size`, `contentType` and `id` of the `file` observable | readonly |
| `parameters` | JSON object of key/value pairs set during job creation | readonly |
| `message` | A free text field to set additional text/context for a job | readonly |
| `tlp` | The TLP of the analyzed observable | readonly |
| `startDate` | Start date | computed |
| `endDate` | End date | computed |
| `createdAt` | Creation date. Please note that a job can be requested but not immediately honored. The actual time at which it is started is the value of `startDate` | computed |
| `createdBy` | User who created the job | computed |
| `updatedAt` | Last update date (only Cortex updates a job when it finishes) | computed |
| `updatedBy` | User who submitted the job and which identity is used by Cortex to update the job once it is finished | computed |

### List and Search
This call allows a user with `read`,`analyze` or `orgAdmin` role to list and search all the analysis jobs made by their organization.

If you want to list all the jobs:
```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/job/_search?range=all'
```

If you want to list 10 jobs:
```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/job/_search'
```

If you want to list 100 jobs:
```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/job/_search?range=0-100'
```

If you want to search jobs according to various criteria:
```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/job/_search' -d '{
  "query": {
    "_and": [
      {"status": "Success"},
      {"dataType": "ip"}
    ]
  }
}'
```

This call supports the `range` and `sort` query parameters declared in [paging and sorting details](#paging-and-sorting)

### Get Details
This call allows a user with `read`,`analyze` or `orgAdmin` role to get the details of a job. It does not fetch the job report.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/job/JOB_ID'
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
  "updatedBy": "demo"
}
```

### Get Details and Report
This call allows a user with `read`,`analyze` or `orgAdmin` role to get the details of a job including its report.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/job/JOB_ID/report'
```

It returns a JSON response with the structure below. If the job is not yet completed, the `report` field contains a string representing the job status:

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

### Wait and Get Job Report
This call is similar the one described above but allows the user to provide a timeout to wait for the report in case it is not available at the time the query was made:

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/job/JOB_ID/waitreport?atMost=1minute'
```

The `atMost` is a duration using the format `Xhour`, `Xminute` or `Xsecond`.

### Get Artifacts
This call allows a user with `read`,`analyze` or `orgAdmin` role to get the extracted artifacts from a job if such extraction has been enabled in the corresponding analyzer configuration. Please note that extraction is imperfect and you might have inconsistent or incorrect data.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/job/JOB_ID/artifacts'
```

It returns a JSON array with the following structure:

```json
[
  {
    "dataType": "ip",
    "createdBy": "demo",
    "data": "8.8.8.8",
    "tlp": 0,
    "createdAt": 1525432900553,
    "id": "AWMq4tvLjidKq_asiwcl"
  }
]
```

### Delete
This API allows a user with `analyze` or `orgAdmin` role to delete a job:

```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/job/JOB_ID'
```

This marks the job as `Deleted`. However the job's data is not removed from the  database.

## Analyzer APIs
The following section describes the APIs that allow manipulating analyzers.

### Analyzer Model
An analyzer is defined by the following attributes:

| Attribute | Description | Type |
| --------- | ----------- | ---- |
| `id` | Analyzer ID once enabled within an organization | readonly |
| `analyzerDefinitionId`| Analyzer definition name | readonly |
| `name` | Name of the analyzer | readonly |
| `version` | Version of the analyzer | readonly |
| `description` | Description of the analyzer | readonly |
| `author` | Author of the analyzer | readonly |
| `url` | URL where the analyzer has been published | readonly |
| `license` | License of the analyzer | readonly |
| `dataTypeList` | Allowed datatypes | readonly |
| `baseConfig` | Base configuration name. This identifies the shared set of configuration with all the analyzer's flavors | readonly |
| `jobCache` | Report cache timeout in minutes, visible for `orgAdmin` users only | writable |
| `rate` | Numeric amount of analyzer calls authorized for the specified `rateUnit`, visible for `orgAdmin` users only | writable |
| `rateUnit` | Period of availability of the rate limite: `Day` or `Month`, visible for `orgAdmin` users only | writable |
| `configuration` |  A JSON object where key/value pairs represent the config names, and their values. It includes the default properties `proxy_http`, `proxy_https`, `auto_extract_artifacts`, `check_tlp`, and `max_tlp`, visible for `orgAdmin` users only | writable |
| `createdBy` | User who enabled the analyzer | computed |
| `updatedAt` | Last update date | computed |
| `updatedBy` | User who last updated the analyzer | computed |

### Enable
This call allows a user with an `orgAdmin` role to enable an analyzer.

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/organization/analyzer/:analyzerId' -d '{
  "name": "Censys_1_0",
  "configuration": {
    "uid": "XXXX",
    "key": "XXXXXXXXXXXXXXXXXXXX",
    "proxy_http": "http://proxy:9999",
    "proxy_https": "http://proxy:9999",
    "auto_extract_artifacts": false,
    "check_tlp": true,
    "max_tlp": 2
  },
  "rate": 1000,
  "rateUnit": "Day",
  "jobCache": 5
}'
```

### List and Search
These calls allow a user with a `analyze` or `orgAdmin` role to list and search all the enabled analyzers within the organization.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/analyzer'
```

or

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/analyzer/_search' -d '{
  "query": {}
}'
```

Both calls supports the `range` and `sort` query parameters declared in [paging and sorting details](#paging-and-sorting), and both return a JSON array of analyzer objects as described in [Analyzer Model section](#analyzer-model).

If called by a user with only an `nalyzer` role, the `configuration` attribute is not included on the JSON objects.

### Get Details
This call allows a user with a `analyze` or `orgAdmin` role to get an analyzer's details.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/analyzer/ANALYZER_ID'
```

It returns a analyzer JSON object as described in [Analyzer Model section](#analyzer-model).

If called by a user with only an `nalyzer` role, the `configuration` attribute is not included on the JSON objects.

### Get By Type
This call is mostly used by TheHive and allows to quickly get the list of analyzers that can run on the given datatype. It requires an `analyze` or `orgAdmin` role.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/analyzer/type/DATA_TYPE'
```

It returns a JSON array of analyzer objects as described in [Analyzer Model section](#analyzer-model) without the `configuration` attribute, which could contain sensitive data.

### Update
This call allows an `orgAdmin` user to update the `name`, `configuration` and `jobCache` of an enabled analyzer.

```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/analyzer/ANALYZER_ID' -d '{
  "configuration": {
    "key": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "polling_interval": 60,
    "proxy_http": "http://localhost:8080",
    "proxy_https": "http://localhost:8080",
    "auto_extract_artifacts": true,
    "check_tlp": true,
    "max_tlp": 1
  },
  "name": "Shodan_Host_1_0",
  "rate": 1000,
  "rateUnit": "Day",
  "jobCache": null
}'
```

It returns a JSON object describing the analyzer as defined in [Analyzer Model section](#analyzer-model).

### Run
This API allows a user with a `analyze` or `orgAdmin` role to run analyzers on observables of different datatypes.

For `file` observables, the API call must be made as described below:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/analyzer/ANALYZER_ID/run' \
  -F 'attachment=@/path/to/observable-file' \
  -F '_json=<-;type=application/json' << _EOF_
  {
    "dataType":"file",
    "tlp":0
  }
_EOF_
```

for all the other types of observerables, the request is:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/analyzer/ANALYZER_ID/run' -d '{
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

To force bypassing the cache, one can add the following query parameter: `force=1`

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'https://CORTEX_APP_URL:9001/api/analyzer/ANALYZER_ID/run?force=1' -d '{
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

### Disable
This API allows an `orgAdmin` to disable an existing analyzer in their organization and delete the corresponding configuration.

```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'https://CORTEX_APP_URL:9001/api/analyzer/ANALYZER_ID'
```

## Miscellaneous APIs

### Paging and Sorting
All the `search` API calls allow sorting and paging parameters, in addition to a query in the request's body. These calls usually have URLs ending with the `_search` keyword but that's not always the case.

The followings are query parameters:

- `range`: `all` or `x-y` where `x` and `y` are numbers (ex: 0-10).
- `sort`: you can provide multiple sort criteria such as: `-createdAt` or `+status`.

Example:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' -H 'Content-Type: application/json' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID/user?range=0-10&sort=-createdAt&sort=+status' -d '{
  "query": {}
}'
```
