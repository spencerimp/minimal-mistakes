---
layout: single
title: Docker-compose and Postgres example
description: ""
category:
tags: [docker, postgres]
permalink: /2017/07/18/docker-compose-and-postgres-example/
---

Docker-compose helps us to set up multi-container services. Which saves us time on linking those containers and fewer commands.

For example, you may have a main program that retrieves data from postgres database, you may set up two individual Docker images, one for the main program and another for the database. Doing this way is straightforward: launch two containers, get the ip and port of the container on which postgres runs. However, we can wrap these container into network using Docker compose.

Let's start with an example Docker Compose file

**docker-compose.yaml**

```
version: "3"

services:
  myapp:
    build:
      context: .
      dockerfile: Dockerfile
    stdin_open: true
    tty: true
    volumes:
      - .:/home/spencer/src

  postgres:
    image: postgres:9.6.3
    environment:
      POSTGRES_DB: spencer_db
      POSTGRES_USER: spencer
      POSTGRES_PASSWORD: password_On_testDB!
```

Here the main program is built as myapp. The stdin_open refers to -i and tty refers to -t when you run **docker run -i -t myadd**

Then, you can build them, run database in the background, sync the current directory on host machine with /home/spencer/src on the container and connect the myapp with interactive shell.

```
docker-compose up -d postgres
docker-compose build myapp
docker-compose run myapp
```

Note:
docker-compose is the only workaround for interactive shell; others such as start will hang forever (which is not supposed to be if we set **stdin_open** and **tty** to true.
Hope Docker offical with fix them soon.

References

[https://stackoverflow.com/questions/42593396/docker-compose-up-is-starting-the-container-and-immediately-stopping-them](https://stackoverflow.com/questions/42593396/docker-compose-up-is-starting-the-container-and-immediately-stopping-them)

[https://stackoverflow.com/questions/36249744/interactive-shell-using-docker-compose/39150040](https://stackoverflow.com/questions/36249744/interactive-shell-using-docker-compose/39150040)




