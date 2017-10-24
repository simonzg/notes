## Use Lua with Nginx

### Install OpenResty

Follow the instructions [HERE](https://openresty.org/en/linux-packages.html) to install OpenResty binary

After installation, you can check with 

```bash
resty -e 'print("hello world")'
```

That should print

```
hello world
```

### Setup Environment

Add these lines to your ~/.bashrc

```bash
export PATH=/usr/local/openresty/bin:/usr/local/openresty/nginx/sbin:$PATH
```

### Prepare for nginx

```bash
mkdir ~/work
cd ~/work
mkdir logs/ conf/
```

Create this file at `conf/nginx.conf`

```nginx
worker_processes  1;
error_log logs/error.log;
events {
    worker_connections 1024;
}
http {
    server {
        listen 8080;
        location / {
            default_type text/html;
            content_by_lua '
                ngx.say("<p>hello, world</p>")
            ';
        }
    }
}
```

### Start nginx for test

Now you can start nginx with:

```bash
nginx -p `pwd` -c conf/nginx.conf
```

and test with

```bash
curl http://localhost:8080/
```

### Use with lua

```bash
cd /usr/local
wget https://github.com/SkyLothar/lua-resty-jwt/releases/download/v0.1.11/lua-resty-jwt-0.1.11.tar.gz
tar xvf lua-resty-jwt-0.1.11.tar.gz
mv lua-resty-jwt-0.1.11 lua-resty-jwt

mkdir nginx-jwt
wget https://github.com/auth0/nginx-jwt/releases/download/v1.0.1/nginx-jwt.tar.gz
tar xvf nginx-jwt.tar.gz -C nginx-jwt
```

Now update your `conf/nginx.conf` into this:

```nginx
worker_processes  1;
error_log logs/error.log;
events {
    worker_connections 1024;
}

env JWT_SECRET;
http {
    lua_package_path '/usr/share/nginx-jwt/?.lua;/usr/share/lua-resty-jwt/?.lua;;';

    server {
        listen 81;

        location /hello {
            default_type text/html;
            content_by_lua '
                ngx.say("<p>hello, world</p>")
            ';
        }

        location / {
          access_by_lua '
            local jwt = require("nginx-jwt")
            jwt.auth()
          ';
          proxy_pass http://127.0.0.1:8090;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_next_upstream error timeout http_502;
        }
    }
}
```

Then you just need to expose `JWT_SECRET` environment variable

```bash
expose JWT_SECRET='some secret'
```

And start you nginx again

```bash
# make sure you already closed all nginx services before this
nginx -p `pwd` -c conf/nginx.conf
```

Then you could test like this:

```bash
curl http://<server-ip>:81/hello

# which returns
<p>hello, world</p>
```

### Auth using Token

First, you could try without token

```bash
curl http://<server-ip>:81/Cam2.webm
```

You could only get something like this:

```
<html>
<head><title>401 Authorization Required</title></head>
<body bgcolor="white">
<center><h1>401 Authorization Required</h1></center>
<hr><center>openresty/1.11.2.5</center>
</body>
</html>
```

You can get token like this: 

```bash
curl -H "Content-Type:application/json" -H "Accept: application/json" -H "Authorization: Basic ZGVmYXVsdGtleTo=" -X POST -d '{"device":"test-device"}' http://authcluster.auroralands.com:7350/user/login

# output should be like this:
{"session":{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1MDg4OTI3NTksImhhbiI6InVoYWtFcWdkWUciLCJ1aWQiOiJhM2IwZTE0ZS1lYjdkLTQwZDAtODMyOC0yZDYwNjdhMjU1OWQifQ.Mfb6b-Xhh4bS8OGLt05EbzM1qRtvZupZLN3ShXHe6Bw"}}
```

Copy the token part (which in this case is `eyJhb...He6Bw` )

And issue a streaming request:

```bash
curl -H "Authorization: Bearer <token>" http://<server-ip>:81/Cam2.webm
```

You should start to get streaming messages
