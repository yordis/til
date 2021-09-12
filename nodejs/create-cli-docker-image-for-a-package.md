# Create CLI Docker Image for a Package

I had to create a Docker Image for a CLI package. I learned how to make it
friendly to use with plain `docker run` and as well as add some nice things that
makes it friendly to use in GitLab.

## Files

I was using a custom NPM registry, and I needed to be able to pull packages for
`@core` scope, so I had to create a `<rootDir>/.npmrc` file.

```text
@core:registry=https://gitlab.example.com/api/v4/packages/npm/
//gitlab.example.com/api/v4/projects/1123/packages/npm/:_authToken=${GITLAB_TOKEN}
//gitlab.example.com/api/v4/packages/npm/:_authToken=${GITLAB_TOKEN}
```

Notice `GITLAB_TOKEN` being injected in the `.npmrc`, therefore, we will need
such environment variable when we install the packages otherwise it will fail.

The `<rootDir>/package.json` file:

```jsonc
{
  "bin": {
    "my-cli-name": "bin/my-cli-name"
  }
}
```

And finally the `<rootDir>/Dockerfile` file:

```dockerfile
# The deps stage only deals with installing packages
FROM node:lts AS deps
# Inject the token used in the `.npmrc` only during the installation, this is
# really good, that means we don't leak the token in the final image, this is
# extremely important for security reasons
ARG GITLAB_TOKEN
WORKDIR /app
COPY package.json yarn.lock .npmrc ./
RUN yarn install --frozen-lockfile

# The builder stage deals with building the package
FROM node:lts AS builder
ARG GITLAB_TOKEN
WORKDIR /app
COPY . .
COPY --from=deps /app/node_modules ./node_modules
# Run the script that builds the code, like compiling with TypeScript
RUN yarn build
# Clean up the `node_modules` directory
RUN yarn install --production --ignore-scripts --prefer-offline

# The runner stage copy the necessary files for production usage and prepare
# the image to be CLI friendly image.
FROM node:lts AS runner
WORKDIR /my-package-name
COPY --from=builder /app/package.json .
COPY --from=builder /app/bin ./bin
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
# This is a trick, `yarn link` will symlink the package as a global install,
# therefore, all the bin registered in the package.json will be available
# globally
RUN yarn link

# If you want to avoid having to type the executable name when running the
# container. Also notice that I use the name of the bin as if it was installed
# globally
ENTRYPOINT ["my-cli-name"]
```

## Using the image with docker

First, build the image

```bash
# Notice that we pass the GITLAB_TOKEN
docker build -t my-cli-image:latest --build-arg GITLAB_TOKEN .
```

Now, we can use the image as a CLI:

```bash
docker run --rm my-cli-image:latest --help
```

## Using the image in GitLab CI

Since we used `yarn link`, all the bins from the packages are available
globally:

```yaml
shortcut:release:
  image: my-cli-image:latest
  script:
    - my-cli-name --help
```
