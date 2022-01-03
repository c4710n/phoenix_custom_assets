# PETAL starter for Phoenix 1.6

> From Phoenix 1.6, PETAL starter only supports umbrella projects.

Initial commit of this project is generated by following command:

```sh
$ mix phx.new hello --app hello --umbrella --no-ecto
```

And I recommend you to create your project with `--umbrella`:

```sh
$ mix phx.new demo --umbrella
```

## Usage

### 1. Replace your `apps/<app>_web/assets` with `apps/hello_web/assets`

```sh
$ rm -rf apps/<app>_web/assets
$ svn export \
  https://github.com/c4710n/PETAL-starter/branches/phx-1.6/apps/hello_web/assets \
  apps/<app>_web/assets
```

> SVN provides `export` function which is useful for copying a sub-directory from a repo.

### 2. Adjust `config/dev.exs` to setup `watchers`

```elixir
config :hello, HelloWeb.Endpoint,
  # ...
  watchers: [
    npm: [
      "run",
      "watch",
      cd: Path.expand("../apps/<app>_web/assets", __DIR__)
    ]
  ]
```

### 3. Remove esbuild related things

Remove `:esbuild` in `apps/<app>_web/mix.exs`:

```diff
- {:esbuild, "~> 0.2", runtime: Mix.env() == :dev},
```

Modify `aliases` in `app/<app>_web/mix.exs`:

```diff
  defp aliases do
    [
-     setup: ["deps.get"],
-     "assets.deploy": ["esbuild default --minify", "phx.digest"]
+     setup: ["deps.get", "assets.deps.get"],
+     "assets.deps.get": "cmd npm install --prefix assets",
+     "assets.deploy": ["cmd npm run deploy --prefix assets", "phx.digest"]
    ]
  end
```

Remove related comments in `config/dev.exs`:

```diff
- For example, we use it with esbuild to bundle .js and .css sources.
```

Remove related config in `config/config.exs`:

```diff
- # Configure esbuild (the version is required)
- config :esbuild,
-   version: "0.12.18",
-   default: [
-     args: ~w(js/app.js --bundle --target=es2016 --outdir=../priv/static/assets),
-     cd: Path.expand("../apps/hello_web/assets", __DIR__),
-     env: %{"NODE_PATH" => Path.expand("../deps", __DIR__)}
-   ]
```

### 4. Final

Clean `mix.lock`:

```sh
$ mix deps.clean --unused --unlock
```
