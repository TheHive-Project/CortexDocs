# Miscellaneous

## Paging and Sorting

All the `search` API calls allow sorting and paging parameters, in addition to a query in the request's body. These calls usually have URLs ending with the `_search` keyword but that's not always the case.

The followings are query parameters:

- `range`: `all` or `x-y` where `x` and `y` are numbers (ex: 0-10).
- `sort`: you can provide multiple sort criteria such as: `-createdAt` or `+status`.

Example:

```bash
curl -XPOST -H 'Authorization: Bearer **API_KEY**' 'http://CORTEX_APP_URL:9001/api/organization/ORG_ID/user?range=0-10&sort=-createdAt&sort=+status -d {
  query: {}
}'