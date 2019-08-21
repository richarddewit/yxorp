# Nginx local development proxy

## Using

- [docker](https://docs.docker.com/install/)
- [mkcert](https://github.com/FiloSottile/mkcert)
- [sly](https://richarddewit.github.io/sly/)
- [hosts](https://github.com/richarddewit/hosts)

## Get started

```bash
# Create Diffie-Hellman (DH) parameters for SSL
$ sly dhparam

# Proxy example.test to 127.0.0.1:8000
$ sly add example.test 8000

# Run the proxy container
$ sly start
```

## How it works

The nginx container runs with `--network="host"` to be able to proxy to any locally running development server like `npm run dev`, `rails s`, `django-admin runserver`, `mix phx.server`, etc.
Just find the port the app is running on, think of a hostname (add it to the app's "allowed hosts" if needed) and create the config for by running `sly add <hostname> <port>` in the nginx-proxy folder.
