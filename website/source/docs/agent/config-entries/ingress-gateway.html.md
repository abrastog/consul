---
layout: "docs"
page_title: "Configuration Entry Kind: Ingress Gateway"
sidebar_current: "docs-agent-cfg_entries-ingress_gateway"
description: |-
  The ingress-gateway config entry kind allows for configuring Ingress gateways with listeners that expose a set of services outside the Consul service mesh.
---

# Ingress Gateway

  The `ingress-gateway` config entry kind allows for configuring Ingress gateways
  with listeners that expose a set of services outside the Consul service mesh.
  See [Ingress Gateway](/docs/connect/ingress_gateway.html) for more information.

## Sample Config Entries

Set up a TCP listener for a single service:

```hcl
kind = "ingress-gateway"
name = "ingress-service"
namespace = "default"

listeners = [
  {
    port = 3456
    protocol = "tcp"
    services = [
      {
        name = "db"
      }
    ]
  }
]
```

Set up a wildcard HTTP listener to proxy traffic to all available services:

```hcl
kind = "ingress-gateway"
name = "ingress-service"
namespace = "default"

listeners = [
  {
    port = 8080
    protocol = "http"
    services = [
      {
        name = "*"
      }
    ]
  }
]
```

## Available Fields

- `Kind` - Must be set to `ingress-gateway`

- `Name` `(string: <required>)` - Set to the name of the service being configured.

- `Namespace` `(string: "default")` - **Enterprise Only** Specifies the namespace the config entry will apply to.

- `Listeners` `(array<IngressListener>: <optional>)` - A list of listeners that
  the ingress gateway should setup, uniquely identified by their port number.

  - `Port` `(int: 0)` - The port that the listener should receive traffic on.

  - `Protocol` `(string: "tcp")` - The protocol associated with the listener.

  - `Services` `(array<IngressService>: <optional>)` - A list of services to be
    exposed via this listener. For "tcp" listeners, only a single service is
    allowed.

    - `Name` `(string: "")` - The name of the service that should be exposed
      through this listener. If the wildcard specifier, `*`, is provided, then
      ALL services will be exposed through the listener. This is not supported
      for listener's with protocol "tcp".

    - `Namespace` `(string: "")` - The namespace to resolve the service from
      instead of the current namespace. If empty the current namespace is
      assumed.

## ACLs

Configuration entries may be protected by
[ACLs](https://learn.hashicorp.com/consul/security-networking/production-acls).

Reading a `ingress-gateway` config entry requires `service:read` on the `Name`
field of the config entry.

Creating, updating, or deleting a `proxy-defaults` config entry requires
`operator:write`.
