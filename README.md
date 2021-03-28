# DoH server with Nginx-Let's Encrypt Proxy and Unbound

Before you launch this, you need a nginx proxy w/ let's encrypt companion as well as web server.

Appropriately locate `config/nginx/vhost/<your_domain>` and `config/nginx/vhost/<your_domain>_location` in the vhost directory of your nginx-proxy to override the `/dns-query` and the root path `/` of your domain.
