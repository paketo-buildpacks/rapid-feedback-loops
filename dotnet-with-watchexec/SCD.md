# Watchexec + .NET Buildpack (SCD)

## Build the app locally (before `pack build`)
The .NET buildpack does not support building a self-contained deployment from
source. Thus, we must build the app on our local system and `pack build` using
the built artifact.

With the .NET SDK installed on your local workstation, run
```bash
dotnet publish --configuration Release --runtime ubuntu.18.04-x64 --self-contained true --output ./build
```

## Prepare SCD for building

Modify the project.toml to match the following:
```
[[build.buildpacks]]
uri = "gcr.io/paketo-buildpacks/watchexec"

[[build.buildpacks]]
uri = "gcr.io/paketo-buildpacks/dotnet-core"

[[build.buildpacks]]
uri = "gcr.io/paketo-community/build-plan"

```

Copy `plan.toml` into the `build` directory:
```bash
cp plan.toml ./build/plan.toml```

## Build with Watchexec

From the `build` directory (output from the previous command), run:
```bash
pack build dotnet-watchexec-scd --descriptor ../project.toml```

## Running

```bash
docker run --name dotnet-watchexec-container-scd \
           --entrypoint launcher --interactive --tty \
           --env PORT=8080 --publish 8080:8080 \
           dotnet-watchexec-scd \
           watchexec --verbose --restart --watch /workspace '/workspace/aspnet --urls http://0.0.0.0:${PORT:-8080}'
```

## Viewing

`curl http://localhost:8080`


## Rebuild the app locally (before copying into container)
If you'd like to modify the app and rebuild it, first remove the output
directory (`build`), and re-run `dotnet publish`:

```bash
dotnet publish --configuration Release --runtime ubuntu.18.04-x64 --self-contained true --output ./build
```

Alternatively, you may specify a separate build directory (e.g. build2) in the
above command. This will, however, result in a second `build` directory nested
within your output directory.

```bash
docker cp ./build/ dotnet-watchexec-container-scd:/build
```

`docker cp` does not currently support copying multiple files at once.
Therefore, it is necessary to run the following command to copy the content of build
into the workspace.

```bash
docker exec -it dotnet-watchexec-container-scd sh -c "cp "/build/*" /workspace"
```
