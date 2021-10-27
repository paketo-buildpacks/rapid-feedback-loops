# Node.js Server App w/ NPM

## Getting Started

### Prerequisites
1. `tilt` CLI + k8s cluster
1. `pack` CLI

Run `tilt up` from this directory to stand up the deployment.

## Hot Reloading

Make some code changes in `server.js` (some snippets have been
commented out for you already) and save the file.

To view the changes either `curl http://localhost:8080` or open the page in your browser (also possible through the Tilt dash.

Try adding the `chalk` package to the project using NPM. Run `npm install chalk` from the local app directory. The section below may
explain any strange behaviour that occurs.

## Limitations 

Adding packages to Node.js projects is a common part of the development
workflow. Unfortunately, NPM does not provide a good mechanism for specifying a
custom node_modules location outside of the current app directory.  Therefore,
if Tilt is set up to sync the entire app directory, the node_modules folder
will be synced each time a package is added/removed via `npm install`. It
appears that, in some cases, the sheer amount of files being synced to the
container whenever `npm install` is run overwhelms the container engine. This
may result in the application crashing and a full rebuild. 

## Recommendations

- When BP_LIVE_RELOAD_ENABLED is set to true, the `npm-start` buildpack should
  invoke whatever command it would have set via watchexec.

- The watchexec start command should be configured such that it restarts the
  given process whenever files in the workspace are changed.

- The watchexec start command should be configured such that it ignores file
  modification events which occur within the `node_modules/.cache` so that web
  frameworks which modify the cache on-the-fly do not trigger a restart. 
