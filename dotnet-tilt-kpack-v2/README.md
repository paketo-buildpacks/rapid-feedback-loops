# Prototype: .NET, Tilt + Paketo Buildpacks w/ Hot Reload

This sample app shows how a simple ASP.NET project can work with the watchexec
buildpack and Tilt. It uses a modified version of the dotnet-execute buildpack
(stored in `.buildpackage/dotnet-execute.cnb`) that sets a start command using
`watchexec`.

## Setting up
### Prerequisites
1. `tilt` + a connected k8s cluster
2. `pack` CLI

### Stand up the deployment
`tilt up`

## Exploring Hot Reload

Make file changes to things in `src/` and see Tilt automatically recompile the
source code and move it into the container. See `src/Startup.cs` for suggested
changes to demo.

## Viewing

`curl http://localhost:8080`
