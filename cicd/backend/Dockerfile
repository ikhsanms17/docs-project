# --- STAGE 1: BASE ---
ARG NODE_IMAGE=node:lts-alpine

FROM $NODE_IMAGE AS base
RUN apk --no-cache add dumb-init
RUN mkdir -p /home/node/app && chown node:node /home/node/app
WORKDIR /home/node/app
USER node
RUN mkdir tmp

# --- STAGE 2: DEPENDENCIES ---
FROM base AS dependencies
COPY --chown=node:node ./package*.json ./
RUN npm ci
COPY --chown=node:node . .

# --- STAGE 3: BUILD ---
FROM dependencies AS build

RUN node ace build

# --- STAGE 4: DEVELOPMENT ---
FROM base AS development

ENV NODE_ENV=development
ENV PORT=3333
ENV HOST=0.0.0.0
COPY --chown=node:node ./package*.json ./
RUN npm ci
COPY --chown=node:node --from=build /home/node/app/build .
EXPOSE 3333
CMD [ "dumb-init", "node", "bin/server.js" ]
