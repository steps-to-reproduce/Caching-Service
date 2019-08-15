# Introduction

Caching-Service is a suite docker based generic cache, authoritative dns server and SNI proxy. This can be used to reduce latency in the internal LAN and avoid repeatative usage of the external access link. This can also be used to mimic a mirror server by using an authoritative server.

# Usage

To use Cacheing-Sevice on your internal LAN, you would just need to set up Static IPs for each cache domain, then run an instance of `testing/generic_cache:latest` (instruction are [here](https://github.com/steps-to-reproduce/Caching-Service/blob/master/Generic_cache/README.md)), `testing/light_dns:latest` (instruction are [here](https://github.com/steps-to-reproduce/Caching-Service/blob/master/Light_DNS/README.md)) on each of the static IPs and an instance of `testing/sni_proxy:latest` bound to port 443 of the docker host.
