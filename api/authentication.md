# Authentication

Most API calls require authentication. Credentials can be provided using a session cookie, an API key or directly using HTTP basic
authentication if this method is specifically enabled.

Session cookies are suitable for browser authentication. We recommend authenticating with API keys when calling the Cortex APIs.

API keys can be generated using the Web UI. To do so, connect using `orgAdmin` account then click on *Organization* and then on the `Create API Key` button in the row corresponding to the user you intend to use for API authentication. Once the API key has been created, click on `Reveal` to display the API key then click on the *copy to clipboard* button if you wish to copy the key to your system's clipboard.

If the user is not yet created, start by clicking on `Add user` to create it then follow the steps mentioned above.

**Alternate method:** you can use a `superAdmin` account to achieve the same result. Once authenticated, and then on *Users* then click on the `Create API Key` button in the row corresponding to the user you intend to use for API authentication. Please **make sure the user is in the right organization** by thoroughly reading its name, which is shown below the user name. Once the API key has been created, click on `Reveal` to display the API key then click on the *copy to clipboard* button if you wish to copy the key to your system's clipboard.

## Usage

Once you have a API key, you can use it, for example, to list the Cortex jobs thanks to the following `curl` command:

```bash
# Using API key
curl -H 'Authorization: Bearer ***API*KEY***' http://CORTEX_APP_URL:9001/api/job
```

## Basic Authentication

Cortex also supports basic authentication which is disabled by default. If you absolutely need to use it, you can enable it by adding `auth.method.basic=true` in the configuration file (`/etc/cortex/application.conf` by default) and restarting the Cortex service.

Once this is done, you can, for example, list the Cortex jobs using the following `curl` command:


```bash
# Using basic authentication
curl -u mylogin:mypassword http://CORTEX_APP_URL:9001/api/job
```