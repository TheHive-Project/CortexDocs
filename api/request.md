# Request formats

Cortex accepts several parameter formats within a HTTP request. They can be used indifferently. Input data can be:

- a query string
- URL-encoded form
- multi-part
- JSON

Hence, the requests below are equivalent.

## Query String

```bash
curl -XPOST 'http://127.0.0.1:9001/api/login?user=me&password=secret'
```

## URL-encoded Form

```bash
curl -XPOST 'http://127.0.0.1:9001/api/login' -d user=me -d password=secret
```

## JSON

```bash
curl -XPOST http://127.0.0.1:9001/api/login -H 'Content-Type: application/json' -d '{
  "user": "me",
  "password": "secret"
}'
```

## Multi-part

```bash
curl -XPOST http://127.0.0.1:9001/api/login -F '_json=<-;type=application/json' << _EOF_
{
  "user": "me",
  "password": "secret"
}
_EOF_
```

## Response Format

Cortex outputs JSON data.