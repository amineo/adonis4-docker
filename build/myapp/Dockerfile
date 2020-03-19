# [ Stage 1 ] - install node modules
FROM node:12.2-alpine as builder
RUN apk update

WORKDIR /build

COPY ./app/myapp/package.json /build/package.json
COPY ./app/myapp/package-lock.json /build/package-lock.json

RUN npm install
RUN npm i --global @adonisjs/cli

RUN npx adonis key:generate --echo

# ============================
# ============================

# [ Stage 2 ] 
FROM node:12.2-alpine
LABEL maintainer="Anthony Mineo <anthonymineo@gmail.com>"

RUN apk update && apk add --no-cache bash curl

# Default envs as prod
ENV ENV_SILENT=true \
    HOST=0.0.0.0 \
    PORT=8080 \
    HASH_DRIVER=bcrypt \
    NODE_ENV=production \ 
    CACHE_VIEWS=true \
    APP_NAME=AdonisJs
ENV APP_URL=http://${HOST}:${PORT}

#ENV APP_KEY=you-need-to-generate-this
# secrets path _FILE prefix or ENV K=V

COPY ./build/myapp/entrypoint.sh /entrypoint.sh

# Setup pm2 as our node process manager
# https://pm2.keymetrics.io/docs/usage/docker-pm2-nodejs/
RUN npm install pm2 -g

# Set node modules outside our app to keep it clean
COPY --from=builder /build/node_modules/ /node_modules/

WORKDIR /app
COPY ./app/myapp /app



EXPOSE 8080


HEALTHCHECK --interval=20s --timeout=30s --start-period=5s --retries=5 \
  CMD curl -f http://localhost:8080/healthz || exit 1

ENTRYPOINT [ "/entrypoint.sh" ]