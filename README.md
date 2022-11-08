# PETAL starter for Phoenix 1.7

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
  https://github.com/c4710n/PETAL-starter/branches/phx-1.7/apps/hello_web/assets \
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

### 3. Remove original esbuild and tailwind related things

Remove `:esbuild` in `apps/<app>_web/mix.exs`:

```diff
- {:esbuild, "~> 0.5", runtime: Mix.env() == :dev},
- {:tailwind, "~> 0.1.8", runtime: Mix.env() == :dev},
```

Modify `aliases` in `app/<app>_web/mix.exs`:

```diff
  defp aliases do
    [
-     setup: ["deps.get"],
-     "assets.deploy": ["esbuild default --minify", "phx.digest"]
+     setup: ["deps.get", "cmd npm install --prefix assets"],
+     "assets.deploy": ["cmd npm run deploy --prefix assets", "phx.digest"]
    ]
  end
```

Remove related config in `config/config.exs`:

```diff
- # Configure esbuild (the version is required)
- config :esbuild,
-   version: "0.14.41",
-   default: [
-     args:
-       ~w(js/app.js --bundle --target=es2017 --outdir=../priv/static/assets --external:/fonts/* --external:/images/*),
-     cd: Path.expand("../apps/<app>_web/assets", __DIR__),
-     env: %{"NODE_PATH" => Path.expand("../deps", __DIR__)}
-   ]
-
- # Configure tailwind (the version is required)
- config :tailwind,
-   version: "3.1.8",
-   default: [
-     args: ~w(
-       --config=tailwind.config.js
-       --input=css/app.css
-       --output=../priv/static/assets/app.css
-     ),
-     cd: Path.expand("../apps/<app>_web/assets", __DIR__)
-   ]
```

Remove related comments in `config/dev.exs`:

```diff
- For example, we use it with esbuild to bundle .js and .css sources.
```

Adjust `apps/<app>_web/.gitignore`:

```diff
 # Ignore assets that are produced by build tools.
-/priv/static/assets/
-
-# Ignore digested assets cache.
-/priv/static/cache_manifest.json
+/priv/static/
```

### 4. Final

Clean `mix.lock`:

```sh
$ mix deps.clean --unused --unlock
```
