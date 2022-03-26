# Get current project version from bash

I was trying to create some `bash` script where I needed to read the `mix`
project version. Here is how I accomplished it:

```bash
CURRENT_PACKAGE_MIX_VERSION=$(mix run --no-compile --no-start -e 'Mix.Project.config() |> Keyword.get(:version) |> IO.puts()')
```

I added `--no-compile` since I didn't need to compile anything. And also
added `--no-start` since I didn't need to start the application (in case there
is one).
