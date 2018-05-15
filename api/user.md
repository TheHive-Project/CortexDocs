# User APIs

This is the set of APIs that allow creating, updating and listing users within an Organization

## User model

A User is defined by the following attributes

- `id`: (*readonly*) the user's id/login
- `name`: (*writable*) the user's name
- `roles`: (*writable*) the users' roles: possible values are: `read`, `read,analyze`, `read,analyze,orgadmin` and `superadmin`
- `status`: (*writable*) the user's status
- `organization`: (*readonly*) the user's organization name, should be set during the creation
- `createdAt`: (*generated*) the creation date
- `createdBy`:  (*generated*) the user who created the user
- `updatedAt`:  (*generated*) the last update date
- `updatedBy`:  (*generated*) the latest user who updated the user
- `hasKey`: (*computed*) true when the user has a defined api key
- `hasPassword`: (*computed*) true if the user has a password

## List API (**Required Roles**: `superadmin`)

This API allows a `superadmin` to list and search all the users of all the organization

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user'
```

or

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user/_search' -d '{
  query: {}
}'
```

Both APIs supports the `range` and `sort` query params declared in [paging and sorting details](misc.md#paging-and-sorting)

## List organizations' users API (**Required Roles**: `orgadmin`, `superadmin`)

Please refer to [Organization APIs](organization.md)

## Create user (**Required Roles**: `superadmin`)

This APIs allows user creation. If it's called by a `superadmin` user, it allows specifying the user's `organization` field.

If called by a `orgadmin` user, the `organization` field's value must be the same as the user who calls the API: `orgadmin` users are allowed to create users only in their organization.

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user' -d '{
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

It returns a JSON object representing the created user as described at [above](#user-model)

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

## Update user

This API allows updating the writable users' fields. It's accessible to users with `superadmin` or `orgadmin` roles.
Every user can also call it to update its own details.

```bash
curl -XPATCH -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user/USER_LOGIN' -d '{
  "name": "John Doe",
  "roles": [
    "read",
    "analyze"
  ],
  status: "Locked"
}'
```

It returns a JSON object representing the updated user as described at [above](#user-model)

## Get user details

This API returns the user details. It's accessible to users with `superadmin` or `orgadmin` roles.
Every user can also call it to read its own details.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user/USER_LOGIN'
```

It returns a JSON object representing the user as described at [above](#user-model)

## Set user password

This API allows setting a user's password. It's accessible to users with `superadmin` or `orgadmin` roles.
Every user can also call it to set its own password.

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user/USER_LOGIN/password/set' -d '{
  "password": "ANY_PASSWORD"
}'
```

and returns 204 response if it succeeds.

## Change user password

This API allows a given user to change only his own existing password. Every user can also call it to change its own password.

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user/USER_LOGIN/password/change' -d '{
  "currentPassword": "password",
  "password": "new-password"
}'
```

and returns 204 response if it succeeds.

## Set and renew user api key

This API allows setting and renewing a user API key. It's accessible to users with `superadmin` or `orgadmin` roles.
Every user can also call it to renew its own API key.

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user/USER_LOGIN/key/renew'
```

and returns the generated API key as `text/plain`response

## Get user api key

This API allows getting a user API key. It's accessible to users with `superadmin` or `orgadmin` roles.
Every user can also call it to get its own API key.

```bash
curl -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user/USER_LOGIN/key'
```

and returns the generated API key as `text/plain`response

## Revoke user api key (**Required Roles**: `superadmin`, `orgadmin`)

This API allows removing a user API key.

```bash
curl -XDELETE -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/user/USER_LOGIN/key'
```

and doesn't return anything