# quickstart

This is a quickstart project for the AlsoSee project if you choose to run it locally with your own data.

It's not perfect, but it should get you up and running quickly and familiarize you with the project.

## Pre-requisites

[Caddy](https://caddyserver.com/download) is a recommended web server to run this project locally.

[Docker](https://docs.docker.com/get-docker/) is required to run the search server locally and build the project.

## Directory Structure

Checkout this project into a directory of your choice (for example, `quickstart`).
Alongside the project directory, create a `media` directory, which will contain your media files (like cover images).

```bash
git clone git@github.com:alsosee/quickstart.git quickstart
mkdir media
```

Inside the project directory checkout `finder` project into the `finder` directory.
It is needed to that it can be passed as Docker context to build the generator, taking into account your schema. Note: "finder" and "generator" terms are used interchangeably.

```bash
cd quickstart
git clone git@github.com:alsosee/finder.git finder
```

So, the directory structure should look like this:

```
├── quickstart             # this project
│   ├── _finder            # reserved directory for the finder-specific files
│   │   ├── templates      # templates for the generator
|   |   |   ├── content.gohtml
│   │   ├── Dockerfile     # Dockerfile for the generator
│   │   └── schema.yml     # schema for files in current project
│   ├── Caddyfile          # Caddy configuration file
│   ├── config.yml         # Finder configuration file
│   ├── docker-compose.yml # Docker Compose configuration file
│   ├── .ignore            # ignore file for the generator
│   ├── finder             # local copy of the finder repository
│   └── ...
├── media                  # directory for media files
├── ...
```

You may override any of the internal `finder` [templates](https://github.com/alsosee/finder/tree/main/templates) by placing them in the `_finder/templates` directory.

Example schema files is provided in the `_finder` directory. You can modify them to match your data structure. As an example, also see the [schema.yml](https://github.com/alsosee/info/blob/main/_finder/schema.yml) used in the main project, and [schema.yml](https://github.com/alsosee/ru/blob/main/_finder/schema.yml) used in the "Ru" project.

## Run Caddy

[Caddyfile](Caddyfile) defines three sites:

- `alsosee.local` - the main site, serves static files from the `output` directory (to be created by the build process)
- `media.alsosee.local` - serves media files from the `media` directory (outside of the project directory)
- `search.alsosee.local` - proxies requests to the search server running on port 7700

First, add them to your `/etc/hosts` file:

```
127.0.0.1       alsosee.local
::1             alsosee.local
127.0.0.1       media.alsosee.local
::1             media.alsosee.local
127.0.0.1       search.alsosee.local
::1             search.alsosee.local
```

Then, start the Caddy server from the project directory:

```bash
caddy run
```

## Build

Start the seach server on port 7700:

```bash
docker-compose up -d search
```

To build the project run the following command:

```bash
docker-compose up --build finder
```

As part of Docker multi-stage build, Docker will first generate code based on your `schema.yml`, then build the generator, and finally run it to generate site in the `output` directory.

Open [http://alsosee.local](http://alsosee.local) in your browser to see the generated site.
