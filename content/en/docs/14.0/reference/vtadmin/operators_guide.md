---
title: Operator's Guide
description: How to configure and run VTAdmin
---

## Get Started

1. Define the [cluster configuration](#cluster-configuration)
2. [Configure `vtadmin-api`](#2-configure-vtadmin-api)
3. [Configure `vtadmin-web`](#3-configure-vtadmin-web)
4. [Build `vtadmin-web`](#4-build-vtadmin-web) 
5. [Deploy](#5-deploy-vtadmin-in-your-environment) `vtadmin-api` and `vtadmin-web` in your environment

The simplest VTAdmin deployment involves a single Vitess cluster. You can look
at the [local example](https://github.com/vitessio/vitess/blob/main/examples/local/scripts/vtadmin-up.sh) for a
minimal invocation of the `vtadmin-api` and `vtadmin-web` binaries.

## 1. Define the cluster configuration

VTAdmin is mapped to one or more Vitess clusters two ways:

- Add a `clusters.yaml` file and pass its path to `vtadmin-api` with the `--cluster-config` build flag
- Set the `--cluster` and/or `--cluster-defaults` [flags](#vtadmin-api-flags) when running `vtadmin-api`

When both command-line cluster configs and a config file are provided, any options for a given cluster on the command-line take precedence over options for that cluster in the config file. 

For a description of the cluster configuration options, see [clusters.example.yaml](https://github.com/vitessio/vitess/blob/main/doc/vtadmin/clusters.yaml).


## 2. Configure `vtadmin-api`

TODO

## 3. Configure `vtadmin-web`

TODO

## 4. Build `vtadmin-web`

TODO 

## 5. Deploy VTAdmin in your environment

TODO
