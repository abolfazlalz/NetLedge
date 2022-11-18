## In development container

### It can be useful before starting.

- [[Gin Auto-Reload]]

### Prepare Dockerfile

At first you have to create your `Dockerfile` In the root path of your project folder.

```dockerfile
FROM golang:alpine

RUN mkdir /app

WORKDIR /app

COPY go.mod .
COPY go.sum .

RUN go mod tidy

COPY . .

RUN go get github.com/codegangsta/gin
RUN go install github.com/codegangsta/gin

ENV BIN_APP_PORT=8080

ENTRYPOINT gin --all -x volume
```

### Prepare Docker compose

Using Docker compose, you can determine the folder linked to the container and the expose port of your system.

```yaml
version: '3'
services:
  web:
    build: .
    ports:
      - "8080:3000"
    volumes:
      - ".:/app"
```

From now on, make any changes in the project code, it will be reloaded automatically.
