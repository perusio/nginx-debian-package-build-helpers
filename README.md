# Building a custom nginx package 

## How to to build the package

### Introduction 

This is the process I use to build my Debian
[package](http://debian.perusio.net). The source is available on
[github](https://github.com/perusio/nginx-debianization). 

The process relies on a couple of scripts I describe below.

### Building the package

 1. Clone the repository at https://github.com/perusio/nginx-debianization.
 2. To update the upstream run:
    
    git-import-orig --uscan
 3. Run the helper scripts below if **there are changes** to any of them:

        nginx_modules
        nginx_scripts
        nginx_deb_configs_put
 4. Update the Debian changelog:

        dch -v "$(git tag | cut -f 2 -d /)-perusio.1.0" "<changelog text>"
    If you want to change the *custom version* from `perusio.1.0` just
    change it to whatever you want. The changelog text is what you
    want to appear in the changelog for the new version. Note that
    the `-v <version>` argument is **only** needed if you create a
    package with a new upstream. Otherwise if it's just a new package
    version **without** a corresponding upstream just do:
    
        dch "<changelog text>"
 5. Go the debian package directory and commit the changed files in git.
 6. Build the package:
 
        git-buildpackage
 7. After the package is built to clean up just issue:
    
        debclean
 8. Done.
        
## Shell scripts for building my Debian package

### Introduction

These are the shell scripts I use to help me building the package.

#### `nginx_modules`

This script is responsible for updating the 3rd party modules used in
the package and installing them in the debian package scaffolding. 

It does the following:

 1. Update all 3rd party modules of the build. The list is defined via
    a file named `module_list.txt` by default.
    
        nginx_modules [-d module install directory] [module list]

    This assumes that the modules are installed in 
    directory. That directory is specified in `vars.sh`. The default
    value is `$HOME/C/nginx`. **Edit** it to reflect your setup.
   
    The script checks out from the upstream source code repository
    using `git` or `svn`. You can modify it use any other version
    control system. It suffices to modify the `nginx_module_install`
    script.

    Note that the modules must have a clone of the original upstream
    repository in the above mentioned directory for the script to
    work.

    The list of modules is defined in `module_list.txt` in the smae
    directory of the script. To use a different modules list pass it
    as the argument `[module list]`. 

 2. Copy all modules defined in the module list to the nginx debian
    package directory.

    The `-d` parameter defines where the module will be installed for
    **building the package**. By default is under
    `$HOME/C/nginx/nginx-debian-package/debian/<module name>`. Where
    the module name is the name of the directory where the module
    repository resides. For example, in `module_list.txt` the Lua
    nginx module directory is named `lua-nginx-module`.

This script invokes the `nginx_module_install` that does the update
and the installation of the module code in the debian package
scaffolding for each module in `module_list.txt`.

#### `nginx_scripts`

This script installs the
[`nginx_ensite`](https://github.com/perusio/nginx_ensite) vhost
enabling/disabling in the debian directory.

    nginx_scripts [install directory] [nginx_ensite source directory]

 * `[install directory]`: the debian directory of the package. By
   default is `$HOME/C/nginx/nginx-debian-package/debian`.
 * `[nginx_ensite source directory]`: the source directory of the
   [`nginx_ensite`](https://github.com/perusio/nginx_ensite). By
   default is `$HOME/shell/nginx-ensite`.

#### `nginx_deb_configs_put`

This script installs my multiple configurations for nginx in the
debian directory.

    nginx_deb_configs_put [install directory] [configs source directory]

 * `[install directory]`: the debian directory of the package. By
   default is `$HOME/C/nginx/nginx-debian-package/debian`.
 * `[configs source directory]`: the base directory of the
 configurations. The default is `$HOME/nginx-config/github`.

The configurations are:

 * [Drupal](https://github.com/perusio/drupal-with-nginx)
 * [Wordpress](https://github.com/perusio/wordpress-nginx)
 * [Squirrelmail](https://github.com/perusio/squirrelmail-nginx)
 * [Chive]https://github.com/perusio/chive-nginx()
 * [Redmine](https://github.com/perusio/redmine-nginx)
 * [Piwik]()https://github.com/perusio/piwik-nginx
