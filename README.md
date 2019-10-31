# eezkorp

A drop-in **nginx reverse proxy** with SSL for *anything* running locally, even Docker containers with a mapped port. **No need** to add some `network` to your existing Docker config. Just `sly add ...` and you're good to go.

If you normally develop using an URL like http://localhost:1337, you can use this proxy to start using https://coolapp.test (note the http**s**, yes there are SSLs!).

## Dependencies

| Tool | Description |
|---|---|
| [docker](https://docs.docker.com/install/) | Run the proxy inside a container |
| [mkcert](https://github.com/FiloSottile/mkcert) | Create and auto-trust self-signed certificates |
| [sly](https://richarddewit.github.io/sly/) | Command line tool using plain Bash functions |
| [emcee](https://github.com/richarddewit/emcee) | Add and remove hostnames in `/etc/hosts` |

## Get started

```bash
# (Run once, takes a while) Create Diffie-Hellman (DH) parameters for SSL
$ sly dhparam

# Proxy coolapp.test to 127.0.0.1:8000
$ sly add coolapp.test 8000

# Run the proxy container
$ sly start
```

Now you can visit https://coolapp.test

### Wildcard domains

You can create a wildcard configuration, however the `/etc/hosts` file doesn't understand that. You'll need to use [`dnsmasq`](http://www.thekelleys.org.uk/dnsmasq/doc.html) to route the base domain or TLD to localhost.

Create the configs and certificates:

```bash
# Escape the asterisk with a \ to avoid possible side-effects
$ sly add \*.coolapp.test 8000
```

Install `dnsmasq` and create a new file named `/etc/NetworkManager/dnsmasq.d/dnsmasq-localhost.conf` with the following contents:

```bash
address=/coolapp.test/127.0.0.1
```

Or when you want to route **all** domains ending with the `.test` TLD:

```bash
local=/test/
address=/test/127.0.0.1
```

Now restart `dnsmasq`:

```bash
# Depending on your OS/Distro, run only one:
$ systemctl restart NetworkManager.service
$ systemctl restart dnsmasq
# Not sure which one to use? Run:
$ systemctl status dnsmasq
# If it's disabled, run the NetworkManager one
```

Now you can visit https://nice.coolapp.test

## How it works

The nginx container runs with `--network="host"` to be able to proxy to any locally running development server like `npm run dev`, `rails s`, `django-admin runserver`, `mix phx.server`, etc.
Just find the port the app is running on, think of a hostname (add it to the app's "allowed hosts" if needed) and create the config for by running `sly add <hostname> <port>` in the **eezkorp** folder.

## Important

- This has only been tested on Linux
- Using `--network="host"`, the Docker container "claims" ports 80 and 443, so make sure no other application is using these ports
