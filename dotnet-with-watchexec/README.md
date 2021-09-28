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
Since `watchexec` is watching all of the `/workspace` directory, it will restart the server when an empty file is copied into `/workspace`

```bash
touch hello.txt
docker cp ./hello.txt  dotnet-watchexec-container:/workspace/hello.txt
```
