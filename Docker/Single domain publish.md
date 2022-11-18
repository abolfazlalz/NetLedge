In **Lotus** Project i have to publish the **Next Js Frontend** and **Laravel Backend** in a subdomain name `club.lotusib.ir`, so i make two url.

For Frontend (Next.Js)
```
https://club.lotusib.ir
```

For backend (Laravel)
```url
https://club.lotusib.ir/api
```

For Panel admin (Laravel again)
```
https://club.lotusib.ir/panel
```

So I created two separate Docker-compose files for two projects and ran them on different ports.

My `docker-compose` look like this:

```yaml
frontend:
	...
	port: 12345
	...
```

And in backend compose:

```yaml
backend:
	...
	port: 6789
	...
```

Then i create a nginx config:

[[Reverse Proxy]]

```nginx
server {
    listen 80;
    listen [::]:80;

    server_name your_domain www.your_domain;
        
    location / {
        proxy_pass http://localhost:12345;
        include proxy_params;
    }
    
    location /api {
        proxy_pass http://localhost:6789;
        include proxy_params;
    }
    
    location /panel {
        proxy_pass http://localhost:6789;
        include proxy_params;
    }
}
```

