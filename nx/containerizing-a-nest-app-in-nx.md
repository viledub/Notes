# Containerizing a Nest app in Nx

To start with I used the nx-container plugin to generate a container step for the application.

```
npm install -D @nx-tools/container-metadata
npm install -D @nx-tools/container
nx g @nx-tools/nx-container:init appName
```

This generates a dockerfile for the appName project and updates the project.json so that the following produces a docker image:

```
nx container appName
```

This works fine, but the dockerfile that we use in this case is dependent on a build step. I wanted to try an alternative approach that would perform the nx build as part of a multistage build. I found this approach [here](https://github.com/jmcdo29/unteris/blob/main/apps/server/Dockerfile) after looking for discussions about containerizing node apps in an Nx monorepo.

First I removed the dependency on the build step from project.json.&#x20;

Here is the docker file:

````
```dockerfile
FROM docker.io/node:lts-alpine as base-node

RUN apk add --no-cache dumb-init=1.2.5-r3

FROM base-node AS prod-common

WORKDIR /src
RUN apk add --no-cache \
	python3 \
	make \
	gcc \
	g++
COPY package*.json \
	tsconfig* \
	nx.json \
	./
RUN npm ci

FROM prod-common AS server-build

COPY apps/ ./apps/
COPY common ./common

RUN npx nx build appName:build:production

FROM base-node AS server-prod

USER node
WORKDIR /src
COPY --from=server-build --chown=node:node /src/dist/apps/appName./
ENV NODE_ENV=production
RUN npm ci
CMD ["dumb-init", "node", "main.js"]
```
````

Discussion

A drawback of this approach is that the prod-common image layer runs an npm ci on the global package.json. This isn't too bad for a couple of applications. The main impact is longer docker image build times. The cache breaks when:

1. A package changes and there is a new package lock.
2. The image is building on an environment without the docker cache.

The resulting image doesn't contain the unnecessary dependencies.&#x20;

#### Comparisons

With the default approach you never have to worry about invalidating the cache because of package.json changes. Nx would be installed on any machine building the project. This approach is/was common in Java Spring applications where the docker build was copying the prebuilt big jar into a java image.

The dockerfile above should work in an nx repo so long as the context is the root of the repo.&#x20;
