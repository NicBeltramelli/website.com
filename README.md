# Demo WordPress website based on Roots stack


## Overview

Trellis will configure a server with the following and more:

- Ubuntu 18.04 Bionic LTS
- Nginx (with optional FastCGI micro-caching)
- PHP 7.4
- MariaDB (a drop-in MySQL replacement)
- SSL support (scores an A+ on the [Qualys SSL Labs Test](https://www.ssllabs.com/ssltest/))
- Let's Encrypt for free SSL certificates
- HTTP/2 support (requires SSL)
- Composer
- WP-CLI
- sSMTP (mail delivery)
- MailHog
- Memcached
- Fail2ban and ferm

Bedrock provides:

- Better WordPress folder structure
- Dependency management with [Composer](https://getcomposer.org)
- Easy WordPress configuration with environment specific files
- Environment variables with [Dotenv](https://github.com/vlucas/phpdotenv)
- Autoloader for mu-plugins (use regular plugins as mu-plugins)
- Enhanced security (separated web root and secure passwords with [wp-password-bcrypt](https://github.com/roots/wp-password-bcrypt))


## Documentation

Full documentation is available at:
- [https://roots.io/docs/trellis/master/installation/](https://roots.io/docs/trellis/master/installation/)
- [https://roots.io/docs/bedrock/master/installation/](https://roots.io/docs/bedrock/master/installation/)


## Requirements

Make sure all dependencies have been installed before moving on:

- PHP >= 7.1
- Composer - [Install](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx)
- [Virtualbox](https://www.virtualbox.org/wiki/Downloads) >= 4.3.10
- [Vagrant](https://www.vagrantup.com/downloads.html) >= 2.1.0

**Windows user?** [Read the Windows getting started docs](https://roots.io/docs/getting-started/windows/#working-with-trellis) for slightly different installation instructions.


## Installation

The directory structure for the project looks like:

```bash
website.com/      # → Root folder for the project
├── trellis/      # → LEMP stack (Trellis)
└── site/         # → WP site (Bedrock)
    └── web/
        ├── app/  # → WordPress content directory (themes, plugins, etc.)
        └── wp/   # → WordPress core (don't touch!)
```

### Get the codebase:

```bash
$ git clone git@github.com:NicBeltramelli/website.com.git && rm -rf website.com/.git
```

### Local development setup:

```bash
$ cd website.com/trellis
$ vagrant up
```
Your local website can be seen at http://website.test/

To access the VM, run vagrant ssh. Site can be found at /srv/www/website.test.

See the Vagrant docs for more commands.

### Remote server setup (staging/production):

**Real projects will be renamed and keept under version control in private repos.**

A base Ubuntu 18.04 (Bionic) server is required for setting up remote servers.

1. Configure your WordPress sites in `group_vars/<environment>/wordpress_sites.yml` and in `group_vars/<environment>/vault.yml` (see the [Vault docs](https://roots.io/docs/trellis/master/vault/) for how to encrypt files containing passwords)
2. Add your server IP/hostnames to `hosts/<environment>`
3. Specify public SSH keys for `users` in `group_vars/all/users.yml` (see the [SSH Keys docs](https://roots.io/docs/trellis/master/ssh-keys/))

For remote servers, installing Ansible locally is an additional requirement. See the [docs](https://roots.io/docs/trellis/master/remote-server-setup/#requirements) for more information

### Provision remote server:

```bash
$ ansible-playbook server.yml -e env=<environment>
```

[Read the remote server docs](https://roots.io/docs/trellis/master/remote-server-setup/) for more information.

### Deploying to remote servers:

1. Add the `repo` (Git URL) of your Bedrock WordPress project in the corresponding `group_vars/<environment>/wordpress_sites.yml` file
2. Set the `branch` you want to deploy (defaults to `master`)

3. Deploy a site:

```bash
$ ./bin/deploy.sh <environment> <site>
```

4. Rollback a deploy (if needed):

```bash
$ ansible-playbook rollback.yml -e "site=<site> env=<environment>"
```

[Read the deploys docs](https://roots.io/docs/trellis/master/deployments/) for more information.
