## Step 1 - Configure Nginx
You need to allow access to Nginx through your firewall. Having set up your server according to the initial server prerequisites, add the following rule with `ufw`:
```bash
sudo ufw allow 'Nginx HTTP'
```
Now you can verify that Nginx is running:
```bash
systemctl status nginx
```
> Abl, you know what is successful result for systemctl status

```
Output● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-08-29 06:52:46 UTC; 39min ago
       Docs: man:nginx(8)
   Main PID: 9919 (nginx)
      Tasks: 2 (limit: 2327)
     Memory: 2.9M
        CPU: 50ms
     CGroup: /system.slice/nginx.service
             ├─9919 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
             └─9920 "nginx: worker process
```

## Step 2 - Configuring your Server Block

It is recommended practice to create a custom configuration file for your new server block additions, instead of editing the default configuration directly. Create and open a new Nginx configuration file using `vim` or your preferred text editor:

```bash
sudo vim /etc/nginx/sites-available/your_domain
```

> For edit in a template file you can copy default configuration
> ```bash
> sudo cp  /etc/nginx/sites-available/default /etc/nginx/sites-available/your_domain
>```

Insert the following into your new file, making sure to replace `your_domain` and `app_server_address`. If you do not have an application server to test with, default to using `http://127.0.0.1:8000` for the optional Gunicorn server setup in **Step 3**:

```
server {
    listen 80;
    listen [::]:80;

    server_name your_domain www.your_domain;
        
    location / {
        proxy_pass app_server_address;
        include proxy_params;
    }
}
```

Save and exit, with `vim` you can do this by `:wq`

This configuration file begins with a standard Nginx setup, where Nginx will listen on port `80` and respond to requests made to your_domain and `www.your_domain`. Reverse proxy functionality is enabled through Nginx’s `proxy_pass` directive. With this configuration, navigating to your_domain in your local web browser will be the same as opening app_server_address on your remote machine. While this tutorial will only proxy a single application server, Nginx is capable of serving as a proxy for multiple servers at once. By adding more location blocks as needed, a single server name can combine multiple application servers through proxy into one cohesive web application.

All HTTP requests come with headers, which contain information about the client who sent the request. This includes details like IP address, cache preferences, cookie tracking, authorization status, and more. Nginx provides some recommended header forwarding settings you have included as `proxy_params`, and the details can be found in `/etc/nginx/proxy_params`:
```
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```

With reverse proxies, your goal is to pass on relevant information about the client, and sometimes information about your reverse proxy server itself. There are use cases where a proxied server would want to know which reverse proxy server handled the request, but generally the important information is from the original client’s request. In order to pass on these headers and make information available in locations where it is expected, Nginx uses the `proxy_set_header` directive.

Here are the headers forwarded by `proxy_params` and the variables it stores the data in:

-   Host: This header contains the original host requested by the client, which is the website domain and port. Nginx keeps this in the `$http_host` variable.
-   X-Forwarded-For: This header contains the IP address of the client who sent the original request. It can also contain a list of IP addresses, with the original client IP coming first, then a list of all the IP addresses of the reverse proxy servers that passed the request through. Nginx keeps this in the `$proxy_add_x_forwarded_for` variable.
-   X-Real-IP: This header always contains a single IP address that belongs to the remote client. This is in contrast to the similar X-Forwarded-For which can contain a list of addresses. If X-Forwarded-For is not present, it will be the same as X-Real-IP.
-   X-Forwarded-Proto: This header contains the protocol used by the original client to connect, whether it be HTTP or HTTPS. Nginx keeps this in the `$scheme` variable.

Next, enable this configuration file by creating a link from it to the `sites-enabled` directory that Nginx reads at startup:

```bash
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```

You can now test your configuration file for syntax errors:

```bash
sudo nginx -t
```

With no problems reported, restart Nginx to apply your changes:

```bash
sudo systemctl restart nginx
```

Nginx is now configured as a reverse proxy for your application server, and you can access it from a local browser if your application server is running. If you have an intended application server but do not have it running, you can proceed to starting your intended application server. You can skip the remainder of this tutorial.


#nginx