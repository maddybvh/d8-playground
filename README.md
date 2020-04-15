# D8 Playground

## Getting started

### Requirements

1. Docksal
    * Install Docksal: ([Linux](https://docs.docksal.io/getting-started/setup/#install-linux)) ([Mac](https://docs.docksal.io/getting-started/setup/#install-macos-docker-for-mac))
      * **IMPORTANT:** Docksal does not currently work with Docker Desktop v2.2.0.0+. If you already have Docker Desktop installed, you may have to downgrade to v2.1.0.3: ([Mac](https://download.docker.com/mac/stable/38240/Docker.dmg)).
1. [Drush Launcher](https://github.com/drush-ops/drush-launcher). Follow the instructions there or try:
    * Run `which drush` to find the path of your drush installation
    * `curl -OL https://github.com/drush-ops/drush-launcher/releases/download/0.6.0/drush.phar`
    * `chmod +x drush.phar`
    * Move drush.phar to the location of your old drush executable and rename to drush with `mv drush.phar /path/to/executable/drush`
1. [Pipe Viewer](http://www.ivarch.com/programs/pv.shtml). `pv`
1. Review this README and follow instructions for local development setup.

### Site installation

Clone this project:

* `git clone git@github.com:maddybvh/d8-playground.git` (GitHub repo)
* `cd d8-playground`
* `fin init` - build your local development environment
* `fin composer install` - install composer dependencies

### Troubleshooting

If you had any issues with the site installation, see the common errors below:

> docker: Error response from daemon: driver failed programming external connectivity on endpoint docksal-vhost-proxy (8fbf838bfaa846c5bc0907ca7835faf2ad5453505988f259ae79cb8551afbc89): Error starting userland proxy: listen tcp 192.168.64.100:443: bind: cannot assign requested address.
ERROR:  Failed starting the proxy service.

Docksal does not currently work with Docker Desktop v2.2.0.0+. If you already have Docker Desktop installed, you may have to downgrade to v2.1.0.3: ([Mac](https://download.docker.com/mac/stable/38240/Docker.dmg)).

> ERROR: for cli  Cannot start service cli: OCI runtime create failed: container_linux.go:346: starting container process caused "process_linux.go:449: container init caused \"rootfs_linux.go:58: mounting \\\"/var/lib/docker/volumes/dori_project_root/_data\\\" to rootfs \\\"/var/lib/docker/overlay2/c611dc00713e30ede5999960959d21583ecc7f3a7736e29a12ce30922e753a76/merged\\\" at \\\"/var/www\\\" caused \\\"stat /var/lib/docker/volumes/dori_project_root/_data: stale NFS file handle\\\"\"": unknown
ERROR: Encountered errors while bringing up the project.

Go to `System Preferences > Security & Privacy > Privacy > Full Disk Access`, click on the plus sign and press `Command-Shift-G` to search for and then add the `/sbin/nfsd` folder.

## Local development

### Get Local web address:

Go to [http://d8-playground.docksal/]()

### Docksal commands

Once local development is installed:

* `fin init` to initially create the local environment and pull a seed database and assets
* `fin import-db` - @todo pull latest seed database from the production environment
* `fin phpcs` - PHP Code Sniffer
* `fin start` - to spin containers up
* `fin stop` - to spin them down
* `fin system stop` - to stop Docksal

`fin help` - to list all available commands.

### Run Drush commands

Remember that you need to install Drush Launcher before attempting these commands.

`fin drush <command>`

See a list of most common Drush commands below:

* `fin drush cr` - clear cache
* `fin drush cim` - import configuration
* `fin drush cex` - export configuration
* `fin drush updb` - run database updates
* `fin drush uli` - generate login link for user 1

### Drush aliases

`drush site:alias @self` will display a list of all available aliases.

### Run Composer commands

Please use composer commands defined within Makefile:

Run composer within the container `fin composer <command>`

Examples below:

* `fin composer install`
* `fin composer update`

### Adding new contributed modules

Run `fin composer require drupal/<module_name> -n --prefer-dist -v`
to add it to the list of requirements in composer.json. Then, use drush to
enable the module by running `fin drush en <module_name>`. Be sure to export
the site configuration and commit that as well.

### Patching contributed module

If you need to apply patches (depending on the project being modified, a pull
request is often a better solution), you can do so with the
[composer-patches](https://github.com/cweagans/composer-patches) plugin.

To add a patch to drupal module foobar insert the patches section in the extra
section of composer.json:
```json
"extra": {
    "patches": {
        "drupal/foobar": {
            "Patch description": "URL or local path to patch"
        }
    }
}
```

### Performance tuning

If the site is running slowly for you locally (i.e pages take more than a few seconds to load on average),
you may be able to improve performance by allocating additional OS resources to Docker. This is particularly relevant on Mac OS.
Specifically, we recommend the suggestions in Step 1 [of this article](https://markshust.com/2018/01/30/performance-tuning-docker-mac/):

> Once you have (at the very least) a quad-core MacBook Pro with 16GB RAM and an SSD, go to Docker > Preferences > Advanced. Set the “computing resources dedicated to Docker” to at least 4 CPUs and 8.0 GB RAM.

Alternatively, set your RAM usage to half of what your computer has available.

Also note that disabling caching or enabling Xdebug locally will both decrease performance.

For more information, see Redmine task [#9605](https://pm.savaslabs.com/issues/9605).

## Composer template for Drupal projects

This project is built using [Drupal Recommended project](https://github.com/drupal/recommended-project)

## Updating Drupal Core

This project will attempt to keep all of your Drupal Core files up-to-date; the
project [drupal-composer/drupal-scaffold](https://github.com/drupal-composer/drupal-scaffold)
is used to ensure that your scaffold files are updated every time drupal/core is
updated. If you customize any of the "scaffolding" files (commonly .htaccess),
you may need to merge conflicts if any of your modified files are updated in a
new release of Drupal core.

Follow the steps below to update your core files.

1. Run `fin composer update drupal/core-recommended --with-dependencies` to update Drupal Core and its dependencies.
1. Run `git diff` to determine if any of the scaffolding files have changed.
   Review the files for any changes and restore any customizations to
  `.htaccess` or `robots.txt`.
1. Commit everything all together in a single commit, so `web` will remain in
   sync with the `core` when checking out branches or running `git bisect`.
1. In the event that there are non-trivial conflicts in step 2, you may wish
   to perform these steps on a branch, and use `git merge` to combine the
   updated core files with your customized files. This facilitates the use
   of a [three-way merge tool such as kdiff3](http://www.gitshah.com/2010/12/how-to-setup-kdiff-as-diff-tool-for-git.html). This setup is not necessary if your changes are simple;
   keeping all of your modifications at the beginning or end of the file is a
   good strategy to keep merges easy.

## Generate composer.json from existing project

With using [the "Composer Generate" drush extension](https://www.drupal.org/project/composer_generate)
you can now generate a basic `composer.json` file from an existing project. Note
that the generated `composer.json` might differ from this project's file.


## FAQ

### I forgot my SSH key passphrase, what should I do?

Refer to the [Recovering your SSH key passphrase](https://help.github.com/en/github/authenticating-to-github/recovering-your-ssh-key-passphrase) guide from Github.

### Should I commit the contrib modules I download?

Composer recommends **no**. They provide [argumentation against but also
workrounds if a project decides to do it anyway](https://getcomposer.org/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md).

### Should I commit the scaffolding files?

The [drupal-scaffold](https://github.com/drupal-composer/drupal-scaffold) plugin can download the scaffold files (like
index.php, update.php, …) to the web/ directory of your project. If you have not customized those files you could choose
to not check them into your version control system (e.g. git). If that is the case for your project it might be
convenient to automatically run the drupal-scaffold plugin after every install or update of your project. You can
achieve that by registering `@composer drupal:scaffold` as post-install and post-update command in your composer.json:

```json
"scripts": {
    "post-install-cmd": [
        "@composer drupal:scaffold",
        "..."
    ],
    "post-update-cmd": [
        "@composer drupal:scaffold",
        "..."
    ]
},
```
### How can I apply patches to downloaded modules?

If you need to apply patches (depending on the project being modified, a pull
request is often a better solution), you can do so with the
[composer-patches](https://github.com/cweagans/composer-patches) plugin.

To add a patch to drupal module foobar insert the patches section in the extra
section of composer.json:
```json
"extra": {
    "patches": {
        "drupal/foobar": {
            "Patch description": "URL or local path to patch"
        }
    }
}
```
### How do I switch from packagist.drupal-composer.org to packages.drupal.org?

Follow the instructions in the [documentation on drupal.org](https://www.drupal.org/docs/develop/using-composer/using-packagesdrupalorg).

### How do I specify a PHP version ?

This project supports PHP 5.6 as minimum version (see [Drupal 8 PHP requirements](https://www.drupal.org/docs/8/system-requirements/drupal-8-php-requirements)), however it's possible that a `composer update` will upgrade some package that will then require PHP 7+.

To prevent this you can add this code to specify the PHP version you want to use in the `config` section of `composer.json`:
```json
"config": {
    "sort-packages": true,
    "platform": {
        "php": "5.6.40"
    }
},
```

