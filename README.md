# Javascript Kubernetes Client information

[![Build Status](https://github.com/kubernetes-client/javascript/workflows/Kubernetes%20Javascript%20Client%20-%20Validation/badge.svg)](https://github.com/kubernetes-client/javascript/actions)
[![Client Capabilities](https://img.shields.io/badge/Kubernetes%20client-Gold-blue.svg?style=flat&colorB=FFD700&colorA=306CE8)](http://bit.ly/kubernetes-client-capabilities-badge)
[![Client Support Level](https://img.shields.io/badge/kubernetes%20client-beta-green.svg?style=flat&colorA=306CE8)](http://bit.ly/kubernetes-client-support-badge)
[![Build and Deploy Docs](https://github.com/kubernetes-client/javascript/actions/workflows/deploy-docs.yml/badge.svg)](https://github.com/kubernetes-client/javascript/actions/workflows/deploy-docs.yml)

The Javascript clients for Kubernetes is implemented in dsad dd 
[typescript](https://typescriptlang.org), but can be called from either
Javascript or Typescript.

For now, the client is implemented for server-side use with node
using the `request` library.

There are future plans to also build a jQuery compatible library but
for now, all of the examples and instructions assume the node client.

# Installation

```console
npm install @kubernetes/client-node
```

# Example code

## List all pods

```javascript
const k8s = require('@kubernetes/client-node');

const kc = new k8s.KubeConfig();
kc.loadFromDefault();

const k8sApi = kc.makeApiClient(k8s.CoreV1Api);

k8sApi.listNamespacedPod('default').then((res) => {
    console.log(res.body);
});
```

## Create a new namespace

```javascript
const k8s = require('@kubernetes/client-node');

const kc = new k8s.KubeConfig();
kc.loadFromDefault();

const k8sApi = kc.makeApiClient(k8s.CoreV1Api);

var namespace = {
    metadata: {
        name: 'test',
    },
};

k8sApi.createNamespace(namespace).then(
    (response) => {
        console.log('Created namespace');
        console.log(response);
        k8sApi.readNamespace(namespace.metadata.name).then((response) => {
            console.log(response);
            k8sApi.deleteNamespace(namespace.metadata.name, {} /* delete options */);
        });
    },
    (err) => {
        console.log('Error!: ' + err);
    },
);
```

## Create a cluster configuration programatically
```javascript
const k8s = require('@kubernetes/client-node');

const cluster = {
    name: 'my-server',
    server: 'http://server.com',
};

const user = {
    name: 'my-user',
    password: 'some-password',
};

const context = {
    name: 'my-context',
    user: user.name,
    cluster: cluster.name,
};

const kc = new k8s.KubeConfig();
kc.loadFromOptions({
    clusters: [cluster],
    users: [user],
    contexts: [context],
    currentContext: context.name,
});
const k8sApi = kc.makeApiClient(k8s.CoreV1Api);
...
```

# Additional Examples and Documentation

There are several more JS and TS examples in the [examples](https://github.com/kubernetes-client/javascript/tree/master/examples) directory.

Documentation for the library is split into two resources:

1. The [Kubernetes API Reference](https://kubernetes.io/docs/reference/) is the source-of-truth for all Kubernetes client libraries, including this one. We suggest starting here!
2. The Typedoc autogenerated docs can be viewed [online](https://kubernetes-client.github.io/javascript) and can also be built locally (see below)

# Compatibility

Prior to the `0.13.0` release, release versions did not track Kubernetes versions. Starting with the `0.13.0`
release, we will increment the minor version whenever we update the minor Kubernetes API version
(e.g. `1.19.x`) that this library is generated from.

Generally speaking newer clients will work with older Kubernetes, but compatability isn't 100% guaranteed.

| client version | older versions | 1.18 | 1.19 | 1.20 |
|----------------|----------------|------|------|------|
|  0.12.x        |       -        |  ✓   |  x   |  x   |
|  0.13.x        |       -        |  +   |  ✓   |  x   |
|  0.14.x        |       -        |  +   |  +   |  ✓   |

Key:

* `✓` Exactly the same features / API objects in both javascript-client and the Kubernetes
  version.
* `+` javascript-client has features or api objects that may not be present in the
  Kubernetes cluster, but everything they have in common will work.
* `-` The Kubernetes cluster has features the javascript-client library can't use
  (additional API objects, etc).
* `x` The Kubernetes cluster has no guarantees to support the API client of
  this version, as it only promises _n_-2 version support. It is not tested,
  and operations using API versions that have been deprecated and removed in
  later server versions won't function correctly.

# Known Issues
* Multiple kubeconfigs are not completely supported.
  Credentials are cached based on the kubeconfig username and these can collide across configs.
  Here is the related [issue](https://github.com/kubernetes-client/javascript/issues/592).

# Development

All dependencies of this project are expressed in its
[`package.json` file](package.json). Before you start developing, ensure
that you have [NPM](https://www.npmjs.com/) installed, then run:

```console
npm install
```

## (re) Generating code

```console
npm run generate
```

## Documentation

Documentation is generated via typedoc:

```
npm run docs
```

To view the generated documentation, open `docs/index.html`

## Formatting

Run `npm run format` or install an editor plugin like https://github.com/prettier/prettier-vscode and https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig

## Linting

Run `npm run lint` or install an editor plugin like https://github.com/Microsoft/vscode-typescript-tslint-plugin

# Testing

Tests are written using the [Chai](http://chaijs.com/) library. See
[`config_test.ts`](./src/config_test.ts) for an example.

To run tests, execute the following:

```console
npm test
```

## Contributing

Please see [CONTRIBUTING.md](CONTRIBUTING.md) for instructions on how to contribute.
