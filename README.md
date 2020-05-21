# weewx-docker 🌩🐳 #

[![GitHub Build Status](https://github.com/felddy/weewx-docker/workflows/build/badge.svg)](https://github.com/felddy/weewx-docker/actions)
[![Total alerts](https://img.shields.io/lgtm/alerts/g/felddy/weewx-docker.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/felddy/weewx-docker/alerts/)
[![Language grade: Python](https://img.shields.io/lgtm/grade/python/g/felddy/weewx-docker.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/felddy/weewx-docker/context:python)

## Docker Image ##

[![MicroBadger Version](https://images.microbadger.com/badges/version/felddy/weewx.svg)](https://hub.docker.com/repository/docker/felddy/weewx)
![MicroBadger Layers](https://img.shields.io/microbadger/layers/felddy/weewx.svg)

This docker container can be used to quickly get a
[WeeWX](http://weewx.com) instance up and running.

This container has the following WeeWX extensions installed:

* [mqtt](https://github.com/weewx/weewx/wiki/mqtt)

## Usage ##

### Install ###

Pull `felddy/weewx` from the Docker repository:

```console
docker pull felddy/weewx
```

### Run ###

The easiest way to start the container is to create a
`docker-compose.yml` similar to the following.  If you use a
serial port to connect to your weather station, make sure the
container has permissions to access the port.  The uid/gid can
be set using the environment variables below.

Modify any paths or devices as needed:

```yaml
---
version: "3.8"

volumes:
  data:

services:
  weewx:
    image: felddy/weewx
    init: true
    restart: "yes"
    volumes:
      - type: bind
        source: ./data
        target: /data
    environment:
      - TIMEZONE=US/Eastern
      - WEEWX_UID=weewx
      - WEEWX_GID=dialout
    devices:
      - "/dev/ttyUSB0:/dev/ttyUSB0"
```

1. Create a directory on the host to store the configuration and database files:

    ```console
    mkdir data
    ```

1. If this is the first time running weewx, use the following command to start
   the container and generate a configuration file:

    ```console
    docker-compose run weewx
    ```

1. The configuration file will be created in the `data` directory.  You should
   edit this file to match the setup of your weather station.

1. When you are satisfied with configuration the container can be started in the
   background with:

    ```console
    docker-compose up -d
    ```

## Upgrading ##

1. Stop the running container:

    ```console
    docker-compose down
    ```

1. Pull the new images from the Docker hub:

    ```console
    docker-compose pull
    ```

1. Update your configuration file (a backup will be created):

    ```console
    docker-compose run weewx --upgrade
    ```

1. Read through the new configuration and verify.
   It is helpful to `diff` the new config with the backup.  Check the
   [WeeWX Upgrade Guide](http://weewx.com/docs/upgrading.htm#Instructions_for_specific_versions)
   for instructions for specific versions.

1. Start the container up with the new image version:

    ```console
    docker-compose up -d
    ```

## Volumes ##

| Mount point | Purpose        |
|-------------|----------------|
| /data    | configuration file and sqlite database storage |

## Environment Variables ##

| Mount point  | Purpose | Default |
|--------------|---------|---------|
| TIMEZONE     | Container [TZ database name](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List) | UTC |
| WEEWX_UID    | `uid` the daemon will be run under | weewx |
| WEEWX_GID    | `gid` the deamon will be run under | weewx |

## Building ##

This Docker container has multi-platform support and requires
the use of the
[`buildx` experimental feature](https://docs.docker.com/buildx/working-with-buildx/).
Make sure to enable experimental features in your environment.

To build the container from source:

```console
git clone https://github.com/felddy/weewx-docker.git
cd weewx-docker
docker buildx build \
  --platform linux/amd64 \
  --build-arg VERSION=4.0.0 \
  --output type=docker \
  --tag felddy/weewx .
```

## Debugging ##

There are a few helper arguments that can be used to diagnose container issues
in your environment.

| Purpose | Command |
|---------|---------|
| Generate the default configuration | `docker-compose run weewx` |
| Upgrade a previous configuration | `docker-compose run weewx --upgrade` |
| Generate a test (simulator) configuration | `docker-compose run weewx --gen-test-config` |
| Drop into a shell in the container | `docker-compose run weewx --shell` |

## Contributing ##

We welcome contributions!  Please see [here](CONTRIBUTING.md) for
details.

## License ##

This project is in the worldwide [public domain](LICENSE).

This project is in the public domain within the United States, and
copyright and related rights in the work worldwide are waived through
the [CC0 1.0 Universal public domain
dedication](https://creativecommons.org/publicdomain/zero/1.0/).

All contributions to this project will be released under the CC0
dedication. By submitting a pull request, you are agreeing to comply
with this waiver of copyright interest.
