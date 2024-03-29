> WARNING:
>
> This project is abandoned.
>
> José Valim mentioned a [build.js](https://github.com/livebook-dev/livebook/blob/8ba6baec3a78e01a07d2e64ed06c6ca4f42fde86/assets/build.js) in this [post](https://elixirforum.com/t/phoenix-and-the-asset-pipeline/60736/3). I will use esbuild in the future.

# phoenix_custom_assets

> Provides an alternative `assets/` of [Phoenix](https://www.phoenixframework.org/) for using [LiveView](https://hexdocs.pm/phoenix_live_view) and [TailwindCSS](https://tailwindcss.com/).

## Features

- Phoenix:
  - assets building powered by [Vite](https://vitejs.dev/)
- LiveView:
  - theme-sensitive progress bar
- TailwindCSS:
  - preinstalled plugins:
    - `@tailwindcss/typography`
    - `@tailwindcss/forms`
    - `@tailwindcss/aspect-ratio`
    - `@tailwindcss/line-clamp`
  - set [Inter var](https://rsms.me/inter/) as default font
- Heroicons

## Current supported versions of Phoenix

- `1.7.10`
- `1.7.9`
- `1.7.8`
- `1.7.7`
- `1.7.6`
- `1.7.5`
- `1.7.4`
- `1.7.3`
- `1.7.2`
- `1.7.1`

> Umbrella projects are not supported for now.

## How it works?

It works by generating code into the your application instead of acting as a library. The users have complete freedom to modify the code so it works best with their app.

> This idea comes from `mix phx.gen.auth`.

## Getting Start

### 1. replace `assets/`

```
$ rm -rf assets # be careful

# with GNU tar
$ curl -L https://api.github.com/repos/c4710n/phoenix_custom_assets/tarball \
  | tar -xvz --strip-components=1 --wildcards */assets

# with BSD tar
$ curl -L https://api.github.com/repos/c4710n/phoenix_custom_assets/tarball \
  | tar -xvz --strip-components=1 --include="*/assets"
```

### 2. remove original esbuild and tailwind related code

Remove `:esbuild` and `:tailwind` in `mix.exs`:

```diff
- {:esbuild, "~> 0.5", runtime: Mix.env() == :dev},
- {:tailwind, "~> 0.1.8", runtime: Mix.env() == :dev},
```

Modify `aliases` in `mix.exs`:

```diff
  defp aliases do
    [
       # ...
-      "assets.setup": ["tailwind.install --if-missing", "esbuild.install --if-missing"],
-      "assets.build": ["tailwind default", "esbuild default"],
-      "assets.deploy": ["tailwind default --minify", "esbuild default --minify", "phx.digest"]
+      "assets.setup": ["cmd npm install --prefix assets"],
+      "assets.build": ["cmd npm run build --prefix assets"],
+      "assets.deploy": ["cmd npm run build --prefix assets", "phx.digest"]
    ]
  end
```

Remove related config in `config/config.exs`:

```diff
-# Configure esbuild (the version is required)
-config :esbuild,
-  version: "0.14.41",
-  default: [
-    args:
-      ~w(js/app.js --bundle --target=es2017 --outdir=../priv/static/assets --external:/fonts/* --external:/images/*),
-    cd: Path.expand("../assets", __DIR__),
-    env: %{"NODE_PATH" => Path.expand("../deps", __DIR__)}
-  ]
-
-# Configure tailwind (the version is required)
-config :tailwind,
-  version: "3.2.4",
-  default: [
-    args: ~w(
-      --config=tailwind.config.js
-      --input=css/app.css
-      --output=../priv/static/assets/app.css
-    ),
-    cd: Path.expand("../assets", __DIR__)
-  ]
-
```

Adjust config of watchers in `config/dev.exs`

```elixir
config :hello, HelloWeb.Endpoint,
  # ...
  watchers: [
    npm: [
      "run",
      "watch",
      cd: Path.expand("../assets", __DIR__)
    ]
  ]
```

### 3. Adjust `.gitignore`

```diff
 # Ignore assets that are produced by build tools.
-/priv/static/assets/
-
-# Ignore digested assets cache.
-/priv/static/cache_manifest.json
+/priv/static/
```

### 4. Cleanup `priv/static/`

Just remove it.

### 5. Last

Clean `mix.lock`:

```
$ mix deps.clean --unused --unlock
```

## License

MIT
