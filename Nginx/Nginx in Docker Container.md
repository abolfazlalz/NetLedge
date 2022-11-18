First, let’s create a new directory and `touch` Dockerfile:

```bash
mkdir server && cd server && vim dockerfile
```

```bash
cd server
```

```bash
vim dockerfile
```

Now enter below dockerfile config:

```dockerfile
# Pull the minimal Ubuntu image
FROM ubuntu

# Install Nginx
RUN apt-get -y update && apt-get -y install nginx

# Copy the Nginx config
COPY default /etc/nginx/sites-available/default

# Expose the port for access
EXPOSE 80/tcp

# Run the Nginx server
CMD ["/usr/sbin/nginx", "-g", "daemon off;"]
```

- On this `Dockerfile` we pull a minimal ubuntu and install nginx on it.
- Install nginx on our minimal ubuntu via `apt-get` package managment
- Copy `default` file to `/etc/nginx/sites-available/default` path, Now every time we build our Docker image, Docker copies the config file to the target directory
- Next, we’ll instruct the system to expose the port on which we’ll access our server. In our case, it’s going to be port 80 using TCP:
- Now, we are going to run Nginx whenever our Docker image launches:

then create a new file for nginx configuration:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    
    root /usr/share/nginx/html;
    index index.html index.htm;

    server_name _;
    location / {
        try_files $uri $uri/ =404;
    }
}
```

**Building the Image**

Now, our Dockerfile is ready to be processed. We are going to use the _docker_ command to build our image:

```bash
docker build . -t any-tag-for-image
```

**Running the Image**

Once our image builds successfully, we are going to take it for a test run.

Let’s issue the following command to run the server:

```shell
$ docker run -d -p 80:80 any-tag-for-image
```

#nginx #docker