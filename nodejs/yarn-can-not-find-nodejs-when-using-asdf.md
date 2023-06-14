# Yarn can not find NodeJS when using asdf

For some reason, when installing some NodeJS version using `asdf`; `yarn` is not
able to find the version. I think it is due to `yarn berry`, but I am not sure.

First, find where `yarn` is installed:

```shell
whereis yarn
# yarn: /Users/ubi/.asdf/shims/yarn
```

Open the find:

```shell
vim /Users/ubi/.asdf/shims/yarn
```

And you should see something like:

```shell
/usr/bin/env bash
# asdf-plugin: nodejs 18.16.0

exec /Users/ubi/.asdf/bin/asdf exec "yarn" "$@" # asdf_allow: ' asdf '
```

Notice the section where it says `# asdf-plugin: nodejs 18.15.0`, verify
that the NodeJS version you wanted is not there, and add a new entry with the
new version, for example, adding NodeJS `18.15.0`.

```shell
/usr/bin/env bash
# asdf-plugin: nodejs 18.16.0
# asdf-plugin: nodejs 18.15.0

exec /Users/ubi/.asdf/bin/asdf exec "yarn" "$@" # asdf_allow: ' asdf '
```

Save the changes and it should work.
