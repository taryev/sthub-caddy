<center>

![](.github/sthub-caddy.png)
</center>

# StHub Caddy

Custom [Caddy](https://github.com/caddyserver/caddy) build tailored for my homelab usage.   

Includes following plugins:
- 🔒 [caddy-dns/ovh](https://github.com/caddy-dns/ovh) — OVH DNS challenge support
- 🔁 [mholt/caddy-l4](https://github.com/mholt/caddy-l4) — Layer 4 proxying capabilities

## 🐳 Docker Image

This image is hosted on GitHub Container Registry:

```
ghcr.io/taryev/sthub-caddy:latest
```

## 🚀 Usage with Docker Compose

```yaml
services:
  caddy:
    image: ghcr.io/taryev/sthub-caddy:2
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes:
      - ./conf:/etc/caddy
      - ./site:/srv
      - caddy_data:/data
      - caddy_config:/config
    environment:
      - OVH_APPLICATION_KEY
      - OVH_APPLICATION_SECRET
      - OVH_CONSUMER_KEY

volumes:
  caddy_data:
  caddy_config:
```

## OVH API Application creation and configuration
1. Follow instructions on https://eu.api.ovh.com/createApp/ to create a new API application. For other zones and/or OVH brands, check the [supported APIs](https://github.com/ovh/go-ovh?tab=readme-ov-file#supported-apis) list from OVH's API Go wrapper.

2. Add these permissions, replace `yourdomain.com` by your real domain or by a wildcard to allow access to all your domains.
```
GET /domain/zone/yourdomain.com/record
POST /domain/zone/yourdomain.com/record
GET /domain/zone/yourdomain.com/record/*
PUT /domain/zone/yourdomain.com/record/*
DELETE /domain/zone/yourdomain.com/record/*
GET /domain/zone/yourdomain.com/soa
POST /domain/zone/yourdomain.com/refresh
```

To revoke a key check [this article](https://help.ovhcloud.com/csm/en-gb-api-getting-started-ovhcloud-api?id=kb_article_view&sysparm_article=KB0042784) from OVH's KB.

## 🧪 Example Caddyfile using OVH DNS challenge
```
{
  email admin@yourdomain.com
  acme_dns ovh {
    endpoint ovh-eu
    application_key    {env.OVH_APPLICATION_KEY}
    application_secret {env.OVH_APPLICATION_SECRET}
    consumer_key       {env.OVH_CONSUMER_KEY}
  }
}

wiki.yourdomain.com {
  reverse_proxy wikijs:3000
}
```

## 🔐 Required environment variables

Make sure to provide the following variables (e.g., via .env file, Docker secrets, Portainer stack.env, etc.):

```env
OVH_APPLICATION_KEY=your_key
OVH_APPLICATION_SECRET=your_secret
OVH_CONSUMER_KEY=your_consumer_key
```
