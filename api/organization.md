# Organization APIs

This is the set of APIs that allow creating, updating and listing Cortex organizations

## Organization model

An organization is defined by the following attributes

- `id`: (*readonly*) it is a copy of the name
- `name`: (*readonly*) the organization name
- `status`: (*writable*) the organization status: `Active` or `Locked`
- `description`: (*writable*) the organization description
- `createdAt`: (*generated*) the creation date
- `createdBy`:  (*generated*) the user who created the organization
- `updatedAt`:  (*generated*) the last update date
- `updatedBy`:  (*generated*) the latest user who updated the organization

## List API (**Required Roles**: `superadmin`)

It's possible to list all the organizations

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID/user'
```

or search for a list using a query

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID/user -d {
  query: {"status": "Active"}
}'
```

Both APIs supports the `range` and `sort` query params declared in [paging and sorting details](misc.md#paging-and-sorting)

## Create API (**Required Roles**: `superadmin`)

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization' -d '{
  "name": "demo",
  "description": "Demo organization",
  "status": "Active"
}'
```

## Update API (**Required Roles**: `superadmin`)

Only the organization description and status are updatable

```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID' -d '{
  "description": "New Demo organization",
}'
```

or

```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID' -d '{
  "status": "Active",
}'
```

## Delete API (**Required Roles**: `superadmin`)

Deleting an organization just marks it as `Locked` and doesn't remove the data from the DB

```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID'
```

## Get orgnization details (**Required Roles**: `orgadmin`)

This API returns the details of an organization as described in the [Organization model](#organization-model) section

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID'
```

and should return

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

## List organization users (**Required Roles**: `orgadmin`, `superadmin`)

This API returns the list of **all** the users declared withing an organization. It supports the `range` and `sort` query params declared in [paging and sorting details](misc.md#paging-and-sorting)

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID/user'
```

and should return a list of [Users](user.md#user-model).

If one wants to filter/search for some users (Active ones for example), there is a search API to use as below:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID/user/_search' -d '{
  query: {}
}'
```

It alse supports the `range` and `sort` query params declared in [paging and sorting details](misc.md#paging-and-sorting)

## List organization anlayzers (**Required Roles**: `orgadmin`)

This API returns the list of the analyzers that have been enabled within an organization. Analyzers that are not enabled, are not listed by this API.

It also supports the `range` and `sort` query params declared in [paging and sorting details](misc.md#paging-and-sorting)

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/analyzer' -d '{
  query: {}
}'
```

and should return a list of [Analyzers](analyzer.md#analyzer-model).