# Templates - WordPress Docker

David Williamson @ Varilink Computing Ltd

------

## Purpose

This is the template for the Docker repository for a Varilink WordPress site project. For WordPress site projects, I use three repositories:

1. A WordPress repository that defines the WordPress site itself.
2. A Docker repository that enables the development and testing of the WordPress site on the developer desktop.
3. An Ansible repository that provides playbook for deploying and managing instances of the WordPress site on hosts.

This repository is the template for the second of those types of WordPress site project repositories.

## Contents

| Files / Directories            | Description                                              |
| ------------------------------ | -------------------------------------------------------- |
| `.env`<br>`docker-compose.yml` | Docker Compose project configuration.                    |
| `tools/`                       | Varilink Docker based tools for WordPress site projects. |
| `wordpress/`                   | The template WordPress repository.                       |

## Use

### Accessing the WordPress site on your desktop

Clone this repository locally on your desktop.

```sh
git clone --recurse-submodules git@github.com:varilink/templates_wordpress-docker.git
```

By default this will clone the repository into the directory `template_wordpress-docker` locally. If you want to clone it into a directory with a different name, which is specific to your WordPress site project, then add that directory name to the end of this command.

Change into the directory that's just been created by that `git clone` command. By default the Docker Compose project that this repository corrects has the Compose project name *wordpress* but again, you can change this to something specific to your WordPress site project by editing the `.env` file to change the value of the `COMPOSE_PROJECT_NAME` environment variable.

Now you can bring up your WordPress site on your desktop using this command:

```sh
docker-compose up wordpress
```

This will automatically start its required dependencies, which are the *db* and *proxy* Docker Compose services that are also defined by this repository.

It may be, particularly if you typically run multiple Docker containers on your desktop, that this command fails with an error message something like this:

```
ERROR: for proxy  Cannot start service proxy: driver failed programming external connectivity on endpoint another_proxy_1 (fff161f1b64ae3cc44e52a551fccd79e1277617b0c44b6db05851021d41ead31): Bind for 0.0.0.0:8080 failed: port is already allocated
```

By default this repository tries to bind the *proxy* service to port `8080` on the localhost interface. This error occurs if there is already another service bound to this port. You can change the port that this repository binds its *proxy* service to by changing the value of the `PROXY_PORT` environment variable in the `.env` file. I have multiple WordPress site projects that are based on this repository and I allocate a unique port number to each to avoid such clashes.

The *proxy* service forwards requests to the *wordpress* and so provides the ability to access the WordPress site with a web browser on the user desktop provided that the web browser is configured to use the *proxy* service. Personally, I do this using the profiles feature of the Firefox web browser. I have a separate profile defined for each WordPress site project and within that profile I configure the network settings to with a manual proxy configuration using the host *localhost* and the port `8080`.

Once you've set this up you can access the WordPress site that has been brought up as a container by this repository at the URL http://wordpress by default, because the *wordpress* service uses the `COMPOSE_PROJECT_NAME` set in the `.env` file for its `container_name`. You can edit the `.env` file to set `COMPOSE_PROJECT_NAME` to something more specific to your project, in which case the URL to access the WordPress site on changes as well accordingly.

### Extending the default behaviour of this repository

If you follow the instructions above then the result will be to install a new, vanilla (as yet, not configured) WordPress site. You can however customise aspects of the WordPress site through configuration. We've already covered the setting of the `COMPOSE_PROJECT_NAME` and `PROXY_PORT` variables and their effect above, 

#### WordPress and MariaDB versions

You can dictate the version numbers of the WordPress and MariaDB software that this repository uses by setting values for the `WORDPRESS_TAG` and `MARIADB_TAG` variables in the `.env` file. As the name of these variables suggests, the values must correspond to valid tags for the *wordpress* and *mariadb* images in the [Docker Hub](https://hub.docker.com/).

In the absence of values set for these variables, which is the initial state of this repository, the images with the tag *latest* will be used. Remember to pull those images again from the [Docker Hub](https://hub.docker.com/) if your local copies have become out of date and you want to bring them back up to date.