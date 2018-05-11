# Miscellaneous

## Paging and sorting

All the `search` (where urls end with `_search`) APIs allows sorting and paging parameters, in addition to a query in request's body.

The followings are query params:

- `range`: `all` or `x-y` where `x` and `y` are numbers (ex: 0-10)
- `sort`: you can provide multiple sort criteria ex: `-createdAt` or `+status`

Example:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://127.0.0.1:9001/api/organization/ORG_ID/user?range=0-10&sort=-createdAt&sort=+status -d {
  query: {}
}'