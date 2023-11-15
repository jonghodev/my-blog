---
title: docker-compose nodejs and mysql
date: 2021-10-12 12:10:76
update: 2021-10-12 12:10:76
tags:
 - docker
description:
---

## docker-compose.yml

```yml
version: '3.8'
services:
  app_api:
    build:
      context: ./
      dockerfile: Dockerfile
    container_name: app_api
    ports:
      - 3000:3000
    restart: always
    depends_on:
      - app_mysql
    networks:
      - app_network
  app_mysql:
    image: mysql:8.0.25
    restart: always
    container_name: app_mysql
    ports:
      - 52000:3306
    networks:
      - app_network
    environment:
      MYSQL_ROOT_PASSWORD: '1234'
      MYSQL_PASSWORD: '1234'
      MYSQL_USER: 'app_user'
      MYSQL_DATABASE: 'app_database'

networks:
  app_network:
    driver: bridge
```

## Dockerfile

```Dockerfile
# the first image use node image as the builder because it has git program
FROM node:14.15 as builder

RUN yarn global add ts-node

ENV DOCKERIZE_VERSION v0.2.0
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz

WORKDIR /app

COPY ./package*.json ./
COPY ./yarn.lock ./

RUN yarn

COPY . .

ENTRYPOINT ./docker-entrypoint.sh
```

## docker-entrypoint.sh

echo "wait db server"
dockerize -wait tcp://app_mysql:3306 -timeout 20s

echo "start node server"
TS_NODE_TRANSPILE_ONLY=true ts-node -r tsconfig-paths/register src/index.ts

## .env in nodejs

```text
DB_URL=app_mysql
```
