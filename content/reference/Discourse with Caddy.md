---
title: "Discourse with Caddy"
date: 2026-06-06
---

Caddy is a super easy to use web server that manages all the boring stuff for you, and because my Oracle always-free server's port HTTP/HTTPS ports (80 and 443) were already in use by Caddy to hook up some webpages for my Minecraft servers, I needed a better way.

## Steps

I will be assuming you already have Caddy setup, alongside that your server/network has some sort of firewall system in place to only allow desired ports. You should also have set up your DNS A record on your domain to redirect to your server's IP address.

1. Add the following to your Caddyfile (`/etc/caddy/Caddyfile`), being sure to replace the domain with yours:
```caddy
discourse.example.com {
    reverse_proxy 127.0.0.1:8080
}
```

2. Follow the [beginner Discourse install guide](https://github.com/discourse/discourse/blob/main/docs/INSTALL-cloud.md#install-discourse-on-a-cloud-server) and run the installer command, completing the configuration as normal. This will fail to complete, as we need to fix the configuration.
```bash
wget -qO- https://raw.githubusercontent.com/discourse/discourse_docker/main/install-discourse | sudo bash
```
- This gets the basic installation of Discourse onto your server, which then lets us edit the configuration to work.

3. Edit the Discourse docker configuration (`/etc/discourse/containers/app.yml`) to have the following edits:
```yml
expose:
  - 8080:80
#  - 443:443
env:
  DISCOURSE_FORCE_HTTPS: true
```
- This removes HTTPS from Discourse (Caddy will be managing that with Let's Encrypt) and remaps the HTTP port to 8080 (so that Caddy can reverse-proxy it to port 80 externally).

4. Finish your Discourse install by running the following command:
```bash
sudo ./launcher rebuild app
```
- You should now be able to follow through the rest of the Discourse install process as normal to use your forum.

Email me (link on [main page](https://awyck.me)) if there's any problems with/fixes needed for this guide.