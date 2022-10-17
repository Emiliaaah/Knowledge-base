# Traefik (proxy)

Traefik is a leading modern reverse proxy and load balancer that makes deploying microservices easy (Just make sure to not look at the docs).

Project Homepage: [Traefik](https://traefik.io/) 
Documentation: [Traefik Docs](https://doc.traefik.io/traefik/)

---

### https back-end

To specify the protocol used on the back-end use the example bellow

```yml
- "traefik.http.services.<service>.loadbalancer.server.scheme=https"
```