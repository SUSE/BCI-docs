---
Title: How To Use With Go
slug: use-with-golang
---

There is [a SLE BCI that you can use with the Go language](https://registry.suse.com/static/bci/golang/index.html). There are a couple different recommended methods to work with Go.

## Don't Ship The Compiler

Go is a compiled language producing a binary as the end result. That means the compiler does not need to ship as part of the images you distribute. Instead, it's recommended that you use the Go image as a builder image.


By not shipping the Go compiler with your application, the attack surface area of your containerized application is lower and the overall image size is much smaller.

## Using Go As A Builder Image

There are two ways to work with Go images. First, you can encapsulate your application in a [`scratch`](https://hub.docker.com/_/scratch/) container image. This is essentially a no-op image which is empty. If your Go application depends on libc it won't function when built upon a `scratch` image as libc, or anything else, is not available.

A second method is to use a slim base container image with just the minimal inclusions needed. The [SLE BCIs offer 4 options](https://documentation.suse.com/sles/15-SP4/html/SLES-all/cha-bci.html) with various different inclusions (see the [table highlighting the main differences](https://documentation.suse.com/sles/15-SP4/html/SLES-all/cha-bci.html#bci-variants-table)).

### Building For `scratch`

The following `Dockerfile` contents illustrate building your application using the SLE BCI Go image to build the binary and then putting the resulting binary in a new image based on scratch. The example uses a "hello world" name which you can substitute for your own application.

```
# Build the Go Binary using the SLE BCI Go 1.18 images
FROM registry.suse.com/bci/golang:1.18 as build

WORKDIR /app

COPY go.mod ./
COPY go.sum ./
RUN go mod download

COPY *.go ./

# Make sure to build the application with CGO disabled. This will force Go to
# use some Go implementations of code rather than those normally supplied by the
# host operating system. You need this for scratch images as those supporting
# libraries are not available.
RUN CGO_ENABLED=0 go build -o /hello-world

# Create image to bundle app
FROM scratch

COPY --from=build /hello-world /hello-world

CMD ["/hello-world"]
```

If your application exposes network ports or should run as a non-root user that can be specified in this last step where `FROM scratch` is being used.

### Building For BCI

If you want to put your resulting image on top of a SLE BCI base you can do that instead of using a `scratch` image. There are benefits of doing this such as having libc or CA certificates easily available. When doing that your `Dockerfile` will look a little different:

```
# Build the Go Binary using the SLE BCI Go 1.18 images
FROM registry.suse.com/bci/golang:1.18 as build

WORKDIR /app

COPY go.mod ./
COPY go.sum ./
RUN go mod download

COPY *.go ./

RUN go build -o /hello-world

# Create image to bundle app
FROM registry.suse.com/bci/bci-micro:15.4

COPY --from=build /hello-world /usr/local/bin/hello-world

CMD ["/usr/local/bin/hello-world"]
```

This examples uses a BCI micro image as the base image for the resulting application. This is just one of the options. You can learn more about which to pick in the [SUSE Containers Guide](https://documentation.suse.com/sles/15-SP4/html/SLES-all/cha-bci.html#bci-variants-table).
