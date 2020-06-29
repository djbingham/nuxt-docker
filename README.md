# Nuxt Docker
This is a test repository for debugging issues running Nuxt in a Docker container.

## Dependencies
The only dependency of this project is Docker. The scripts provided will run Node and NPM commands inside standalone Docker containers.

## Usage
To run Nuxt dev mode in a standalone container, clone this repository and run:
```
script/prepare
script/dev
```

This will install dependencies then start a Nuxt dev server on port 80 in a Docker container. The `script/dev` script attaches to the started container, showing the log output. If you `ctrl + c` out of this, the container will be stopped and removed to avoid clogging up your system with old containers.
