+++
date = "2016-07-16T11:00:01+01:00"
draft = false
title = "Kong Headers and Information Leakage"

+++

I am currently researching the hardening the Kong API gatway to get it ready for some penetration tests and wanted to resolve the following issue of information leakage in the response headers.

As you can see Kong sends back a variety of headers that expose that the server is "Kong".

```
'Server': 'Kong/x.x',
'Via': 'kong/x.x',
'X-kong-proxy-latency': '0',
'X-kong-upstream-latency': '79'}
```

There appears to be 2 releated issues open on the Kong GitHub [here](https://github.com/Mashape/kong/issues/1315) and [here](https://github.com/Mashape/kong/issues/1009).

I initially thought that the Server header could be removed by using the nginx setting of setting [server tokens](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_tokens) to false but found that these are explictly set in the Kong core `header_filter()` [here](https://github.com/Mashape/kong/blob/master/kong/core/handler.lua).

Whilst there are open tickets to resolve the quickest way to patch this in that I found was to modify the  `/etc/kong/kong.yml` file to modify the nginx/OpenResty configuration with 4 Lua statements to set these headers to nothing.

```
        # Add additional response headers
        header_filter_by_lua_block {
           kong.header_filter()
           ngx.header["Server"] = nil
           ngx.header["Via"] = nil
           ngx.header["X-Kong-Proxy-Latency"] = nil
           ngx.header["X-Kong-Upstream-Latency"] = nil
        }
```
The fix is likely to add a configuration setting to not set these headers in the first place inside the `header_filter()` although they may still be useful metrics for logs or the StatsD plugin.