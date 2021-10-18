# Dotnet Core Sample App using ASPNet

Copied from [paketo-buildpacks/samples](https://github.com/paketo-buildpacks/samples/blob/cd2e9027eddeb6e96c6e3610195940424438b992/dotnet-core/aspnet)

## Building with Watchexec

```bash
pack build dotnet-watchexec --descriptor project.toml```

## Running

```bash
docker run --name dotnet-watchexec-container \
           --entrypoint launcher --interactive --tty \
           --env PORT=8080 --publish 8080:8080 \
           dotnet-watchexec \
           watchexec --verbose --restart --watch /workspace '/workspace/aspnet --urls http://0.0.0.0:${PORT:-8080}'
```

## Viewing

`curl http://localhost:8080`

## Test Server Restart
Since `watchexec` is watching all of the `/workspace` directory, it will
restart the server when an empty file is copied into `/workspace`

```bash
touch hello.txt
docker cp ./hello.txt  dotnet-watchexec-container:/workspace/hello.txt
```

## Rebuild the app locally (before copying into container)
With the .NET SDK installed on your local workstation, run
```bash
dotnet publish --configuration Release --runtime ubuntu.18.04-x64 --self-contained false --output ./build
```

This will cross-compile the binary for linux in the same way it happens in the
build container. The output will be dumped in `./build`, which can then be
copied into the container.

```bash
docker cp ./build/ dotnet-watchexec-container:/build
```
`docker cp` does not currently support copying multiple files at once.
Therefore, it is necessary to run the following command to copy the content of build
into the workspace.

```bash
docker exec -it dotnet-watchexec-container sh -c "cp "/build/*" /workspace"
```

# NOTES

* Given that the start command currently set by the dotnet-execute buildpack
  requires shell variable expansion, running watchexec with the --no-shell
  option fails.
