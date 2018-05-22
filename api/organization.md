# Organization APIs

Cortex offers a set of APIs to create, update and list organizations.

## Organization Model

An organization (org) is defined by the following attributes:

| Attribute | Description | Type |
| --------- | ----------- | ---- |
|`id` | Copy of the org's name | readonly |
|`name` | Org's name | readonly |
|`status` | Org's status (`Active` or `Locked`) | writable |
|`description` | Org's description | writable |
|`createdAt` | Creation date of the org | generated |
|`createdBy` | User who created the org | generated |
|`updatedAt` | Last update date of the org | generated |
|`updatedBy` | User who last updated the org | generated |

Please note that `id` and `name` are essentially the same. Also, `createdAt` and `updatedAt` are in *epoch*.

## List
It is possible to list all the organizations using the following API call, which requires the API key associated with a `superAdmin` account:

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization'
```

If instead, you'd like to display all the users belonging to the organization which `id` is `ORG_ID`:

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID/user'
```

You can also search for all active users under that organization using the following query:

**FIXME** THE_FOLLOWING_EXAMPLE_DOES_NOT_WORK: ERROR curl: (3) [globbing] nested brace in column 70
```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID/user -d {
  query: {"status": "Active"}
}'
```

Both APIs supports the `range` and `sort` query parameters described in [paging and sorting details](misc.md#paging-and-sorting).

## Create API
It is possible to create an organization using the following API call, which requires the API key associated with a `superAdmin` account:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization' -d '{
  "name": "demo",
  "description": "Demo organization",
  "status": "Active"
}'
```

## Update
You can update an organization's description and status (`Active` or `Locked`) using the following API call which requires the API key associated with a `superAdmin` account:

**FIXME**
```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID' -d '{
  "description": "New Demo organization",
}'
```
or

**FIXME**
```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID' -d '{
  "status": "Active",
}'
```

## Delete
Deleting an organization just marks it as `Locked` and doesn't remove the associated data from the DB. To "delete" an organization, you can use the API call shown below. It requires the API key associated with a `superAdmin` account.

**FIXME**
```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID'
```

## Obtain Details
This API call returns the details of an organization as described in the [Organization model](#organization-model) section.

**FIXME**
```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID'
```

Let's assume that the organization we are seeking to obtain details about is called *demo*. The `curl` command would be:

**FIXME**
```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID'
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

## List Users
As mentioned above, you can use the API to return the list of **all** the users declared withing an organization. For that purpose, use the API call shown below with the API key of an `orgAdmin` or `superAdmin` account. It supports the `range` and `sort` query parameters declared in [paging and sorting details](misc.md#paging-and-sorting).

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID/user'
```

and should return a list of [Users](user.md#user-model).

If one wants to filter/search for some users (Active ones for example), there is a search API to use as below:

**FIXME**
```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID/user/_search' -d '{
  query: {}
}'
```

It also supports the `range` and `sort` query parameters declared in [paging and sorting details](misc.md#paging-and-sorting).

## List Analyzers (**Required Roles**: `orgadmin`)
To list the analyzers that have been enabled within an organization, use the following API call with the API key of an `orgAdmin` user:

**FIXME**
```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/analyzer' -d '{
  query: {}
}'
```

It should return a list of [Analyzers](analyzer.md#analyzer-model).

Please note that this API call does not display analyzers that are disabled. It supports the `range` and `sort` query parameters declared in [paging and sorting details](misc.md#paging-and-sorting).

