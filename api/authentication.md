# Authentication

Most API calls require authentication. Credentials can be provided using a session cookie, an API key or directly using HTTP basic
authentication (when enabled).

Session cookie is suitable for browser authentication. We recommend the use API key authentication. API keys can be generated using the Web interface of the product, under the user admin sections.

For example, to list jobs, use the following curl command:

```bash
# Using API key
curl -H 'Authorization: Bearer ***API*KEY***' http://127.0.0.1:9001/api/job
```

Cortex also supports basic authentication (disabled by default). You can enable it by adding `auth.method.basic=true` in the configuration file.

```bash
# Using basic authentication
curl -u mylogin:mypassword http://127.0.0.1:9000/api/job
```