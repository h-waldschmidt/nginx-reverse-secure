# nginx-reverse-secure

NGINX reverse proxy configuration with two static webservers using Let’s Encrypt.

This configuration was created for an assignment in the ICWA course of the University of Stuttgart.

## Setup

**Important:** When using Let's Encrypt as a certificate authority you can't create a certificate for an IP address.
Let's Encrypt issues certificates only for domains.

1. Make sure to change all occurrences of "example.org" in `proxy.conf`.

2. Start reverse proxy and servers:

   ```sh
   docker compose up -d
   ```

   This won't work on the first run, because the reverse proxy configuration expects an ssl certificate which doesn't exist yet.

   So instead you need to comment out the ssl server, which listens to port 443, in `proxy.conf`.

   Run:

   ```sh
   docker compose down
   ```

   This will stop all the docker containers from this configuration.

3. Now we can create our certificate by running the ACME protocol through certbot:

   ```sh
    docker compose run --rm  certbot certonly -d example.org
   ```

   This runs the ACME protocol with the Let's Encrypt certificate authority without changing the proxy configuration.

   It will give two creation options. Either trough standalone or through webroot.
   Choose option 1 since the reverse proxy isn't running.

4. Now the second server in `proxy.conf` can be uncommented and the containers can be started with:

   ```sh
    docker compose up -d
   ```

   Now our Reverse proxy is using HTTPS.

5. We can check it by running:

   ```sh
   curl https://domainname.com/app1
   ```

   This should connect us with the first server through HTTPS.

## Certificate Renewal

Certificates can easily be renewed without any downtime. Just use the following command:

```sh
docker compose run --rm certbot renew
```

This can also be tested without actually renewing the certificate. Just run:

```sh
docker compose run --rm certbot renew --dry-run
```
