---
title: Operator's Guide
description: How to configure and run VTAdmin
---

## Get Started

1. Define the [cluster configuration](#cluster-configuration)
1. [Configure `vtadmin-api`](#vtadmin-api-flags)
1. [Configure and build `vtadmin-web`](#vtadmin-web-build-vars)
1. [Deploy](#deploying) `vtadmin-api` and `vtadmin-web` in your environment

The simplest VTAdmin deployment involves a single Vitess cluster. You can look
at the [local example](https://github.com/vitessio/vitess/blob/main/examples/local/scripts/vtadmin-up.sh) for a
minimal invocation of the `vtadmin-api` and `vtadmin-web` binaries.

## Cluster configuration

VTAdmin is mapped to one or more Vitess clusters two ways:

- Add a `clusters.yaml` file and pass its path to `vtadmin-api` with the `--cluster-config` build flag
- Set the `--cluster` and/or `--cluster-defaults` [flags](#vtadmin-api-flags) when running `vtadmin-api`

When both command-line cluster configs and a config file are provided, any options for a given cluster on the command-line take precedence over options for that cluster in the config file. 

For a description of the cluster configuration options, see [clusters.example.yaml](https://github.com/vitessio/vitess/blob/main/doc/vtadmin/clusters.yaml).


## vtadmin-api Flags

These flags are provided to the `vtadmin-api` process. They are also referenced in [cmd/vtadmin/main.go](https://github.com/vitessio/vitess/blob/main/go/cmd/vtadmin/main.go).

### Common flags

| Name | Required | Type | Default | Definition |
| -------- | --------- | --------- | --------- |--------- |
| `addr` | **Required** | string | ":15000" | The address for `vtadmin-api` to serve on. |
| `lame-duck-duration` | Optional | seconds | 5s | The length of the lame duck period at shutdown. |
| `lmux-read-timeout` | Optional | seconds | 1s | How long to spend connection muxing. | 

### Cluster config flags

One of `cluster`, `cluster-defaults`, or `cluster-config` file is required. Multiple configurations are permitted; precedence is noted below.

| Name | Definition |
| -------- | --------- | 
| `cluster` | Per-cluster configuration. Any values here take precedence over those defined by the `cluster-defaults` and/or `cluster-config` flags. | 
| `cluster-config` | Path to a yaml cluster configuration; see [clusters.example.yaml](https://github.com/vitessio/vitess/blob/main/doc/vtadmin/clusters.yaml). | 
| `cluster-defaults` | Default options for all clusters. |
| `enable-dynamic-clusters` | Defaults to `false`. Whether to enable dynamic clusters that are set by request header cookies or gRPC metadata. | 

### Tracing flags

| Name | Required | Type | Default | Definition |
| -------- | --------- | --------- | --------- |--------- |
| `grpc-tracing` | Optional | boolean | `false` | If true, enables tracing on the gRPC server. |
| `http-tracing` | Optional | boolean | `false` | If true, enables tracing on the HTTP server. |
| `tracer` | Optional | string | "noop" | Which tracing service to use; see [go/trace/trace.go](https://github.com/vitessio/vitess/blob/main/go/trace/trace.go). |
| `tracing-enable-logging` | Optional | boolean | `false` | Whether to enable logging in the tracing service; see [go/trace/trace.go](https://github.com/vitessio/vitess/blob/main/go/trace/trace.go).  |
| `tracing-sampling-type` | Optional | string | - | Sampling strategy to use for jaeger. Possible values are "const", "probabilistic", "rateLimiting", or "remote"; see [go/trace/plugin_jaeger.go](https://github.com/vitessio/vitess/blob/main/go/trace/plugin_jaeger.go). |
| `tracing-sampling-rate` | Optional | float | 0.1 | Sampling rate for the probabilistic jaeger sampler; see [go/trace/plugin_jaeger.go](https://github.com/vitessio/vitess/blob/main/go/trace/plugin_jaeger.go). |
### gRPC server flags

| Name | Required | Type | Default | Definition |
| -------- | --------- | --------- | --------- |--------- |
| `grpc-allow-reflection` | Optional | boolean | `false` | Whether to register the gRPC server for reflection; this is required to use tools like `grpc_cli`.
| `grpc-enable-channelz` | Optional | boolean | `false` | Whether to enable the [channelz](https://grpc.io/blog/a-short-introduction-to-channelz/) service on the gRPC server. |

### HTTP server flags

| Name | Required | Type | Default | Definition |
| -------- | --------- | --------- | --------- |--------- |
| `http-origin` | **Required** | string | - | repeated, comma-separated flag of allowed CORS origins. omit to disable CORS |
| `http-tablet-url-tmpl` | **Recommended** | string | - | Go template string to generate a reachable http(s) address for a tablet. Currently used to make passthrough requests to /debug/vars endpoints. Example: `"https://{{ .Tablet.Hostname }}:80"` |
| `http-debug-omit-env` | Optional | boolean | `false` | The name of an environment variable to omit from /debug/env, if http debug endpoints are enabled. Specify multiple times to omit multiple env vars. |
| `http-debug-sanitize-env`| Optional | string | - | The name of an environment variable to sanitize in /debug/env, if http debug endpoints are enabled. Specify multiple times to sanitize multiple env vars. | 
| `http-no-compress` | Optional | boolean | `false` | Whether to disable compression of HTTP API responses. |
| `http-no-debug` | Optional | boolean | `false` | Whether to disable `/debug/pprof/*` and `/debug/env` HTTP endpoints | 


### RBAC flags

If using RBAC, both the `--rbac` and `--rbac-config` flags must be set. If not using RBAC, the `--no-rbac` must be set.

| Name | Required | Type | Default | Definition |
| -------- | --------- | --------- | --------- |--------- |
| `no-rbac` | Optional | boolean | false | Whether to disable RBAC. | 
| `rbac` | Optional | boolean | false | Whether to enable RBAC. |
| `rbac-config` | Optional | string | - | Path to an RBAC config file. Must be set if passing `--rbac`. |

### glog flags

See https://pkg.go.dev/github.com/golang/glog.

| Name | Required | Type | Default | Definition |
| -------- | --------- | --------- | --------- |--------- |
| `logtostderr` | Optional | boolean | `false` | If true, logs are written to standard error instead of to files. 
| `alsologtostderr` | Optional | boolean | `false` | If true, logs are written to standard error as well as to files. 
| `stderrthreshold` | Optional | string | `ERROR` | Log events at or above this severity are logged to standard error as well as to files.
| `log_dir` | Optional | string | - | Log files will be written to this directory instead of the default temporary directory. | 
| `v` | Optional | int | 0 | Enable V-leveled logging at the specified level. | 
## vtadmin-web Build Vars 

These environment variables are also referenced in [react-app-env.d.ts](https://github.com/vitessio/vitess/blob/main/web/vtadmin/src/react-app-env.d.ts).

| Name | | Definition |
| -------- | --------- | --------- |
| `REACT_APP_VTADMIN_API_ADDRESS` | **Required** | The full address of `vtadmin-api`'s HTTP interface. Example: "http://127.0.0.1:12345" |
| `REACT_APP_BUGSNAG_API_KEY` | Optional | An API key for https://bugsnag.com, for those using Bugsnag to capture client-side errors. If defined, the [@bugsnag/js](https://www.npmjs.com/package/@bugsnag/js) client will be initialized. Your Bugsnag API key can be found in your Bugsnag Project Settings. | 
| `REACT_APP_BUILD_BRANCH` | Optional | For debugging purposes. The branch that was used to build `vtadmin-web`. If defined, this is displayed on the Debug page. When `REACT_APP_BUGSNAG_API_KEY` is also defined, Bugsnag errors will be annotated with the branch. | 
| `REACT_APP_BUILD_SHA` | Optional | For debugging purposes. The SHA that was used to build `vtadmin-web`. If defined, this is displayed on the Debug page. When `REACT_APP_BUGSNAG_API_KEY` is also defined, Bugsnag errors will be annotated with the SHA.  | 
| `REACT_APP_DOCUMENT_TITLE` | Optional | Used for the document.title property. The default is "VTAdmin". Overriding this can be useful to differentiate between multiple VTAdmin deployments, e.g., "VTAdmin (staging)". |
| `REACT_APP_ENABLE_EXPERIMENTAL_TABLET_DEBUG_VARS` | Optional | Recommended. Defaults to "false". When "true", enables front-end components that query vtadmin-api's /api/experimental/tablet/{tablet}/debug/vars endpoint.| 
| `REACT_APP_FETCH_CREDENTIALS` | Optional | Configures the `credentials` property for fetch requests made against vtadmin-api. If unspecified, uses fetch defaults. See https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch#sending_a_request_with_credentials_included | 
| `REACT_APP_READONLY_MODE` | Optional | Defaults to "false". If "true", UI controls that correspond to write actions (PUT, POST, DELETE) will be hidden. Note that this *only* affects the UI. If write actions are a concern, Vitess operators are encouraged to also configure vtadmin-api for role-based access control (RBAC) if needed; see https://github.com/vitessio/vitess/blob/main/go/vt/vtadmin/rbac/rbac.go | 


## Deploying

TODO
