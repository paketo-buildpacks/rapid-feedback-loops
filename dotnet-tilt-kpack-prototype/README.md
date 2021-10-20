# Prototype: .NET, Tilt, Kpack + Paketo Buildpacks w/ Hot Reload

## Background

This prototype aims to demonstrate the user experience that can be expected once buildpacks are integrated with watchexec and tilt.

A few caveats:
* No code changes were made to the buildpacks themselves.
* This does not represent a finished product, simply an exploration into the possible outcomes of this work.

## Prerequisites

* [`kpack`](https://github.com/vmware-tanzu/kpack-cli) CLI installed locally
* [`kpack`] installed on the cluster: `kubectl apply -f https://github.com/pivotal/kpack/releases/download/v0.3.1/release-0.3.1.yaml
`
* A secret called `registry-credentials` that has push access to the image
  repository
* `tilt`
* `dotnet` CLI
* Kubernetes cluster

## Usage

* `tilt up`
* Make code changes
* Watch it go!




