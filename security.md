# Security Overview

This document describes Swimtopia security principles at a high level.

It does not contain production credentials, hostnames, network topology, Active Directory structure or security-sensitive Cloudflare configuration.

## Areas covered by the platform

### Authentication and access

Production services require authenticated access.

Access boundaries are designed so that public-facing interfaces do not automatically expose internal services.

### Active Directory integration

Directory-related operations are handled through controlled interfaces.

This limits coupling between the application and infrastructure-specific implementation details.

### TLS and certificates

HTTPS and certificate validation are part of the service path.

Certificate issues are treated as system-level problems because they can affect authentication, proxies, tunnels and upstream service connectivity.

### Cloudflare Tunnel

Cloudflare Tunnel is used to provide controlled connectivity to selected services without directly exposing the underlying host.

### Reverse proxy

The reverse proxy is responsible for routing requests to appropriate application services.

Correct public URLs, internal service addresses and callback paths need to remain consistent across the edge, proxy and application layers.

### Secrets

Secrets are not stored in the public repository.

Sensitive values belong in environment-specific configuration or dedicated secret-management mechanisms.

### AI and MCP

AI clients operate through explicitly defined MCP tools.

The model does not receive unrestricted database or infrastructure permissions.

Application-side validation remains responsible for determining whether an operation is authorised.

## Operational lessons

Security-related failures often cross layers.

Examples include problems involving:

- authentication configuration;
- certificates;
- DNS;
- Cloudflare Tunnel;
- reverse proxy routing;
- application URLs;
- service identities;
- upstream directory connectivity.

Troubleshooting therefore requires following the complete request and authentication path rather than treating each component in isolation.
