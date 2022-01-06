# tpoint-flex

This repository stores flex recipes for tpoint plugins. An example plugin is available [here](https://github.com/samm-tpoint/tpoint-plugin-example/).

## What is Symfony Flex?

The official documentation does a good job explaining [Symfony Flex](https://symfony.com/doc/current/setup/flex.html), 
but here's a summary with some extra details: 

- it's a composer plugin included with all SF4+ installs
- whenever a package is installed/removed via composer, the plugin checks if there is a flex "recipe" for this package 
in the official [flex repository](https://github.com/symfony/recipes) or in any private flex recipe repository
- if a recipe is found, it is applied - usually this means registering a new bundle automatically and copying files 
(typically configs - this is why SF4+ has a separate config file for each package instead of one config.yml for 
everything)
- the result of this is that after installing a new package (such as a SF bundle), you don't have to manually copy
config values or register the bundle in bundles.php
- just like composer has composer.lock, flex has symfony.lock - this file must be commited to the repository
- a flex recipe can have multiple versions. There's no documentation on this, but from my tests it seems that the flex 
always selects the latest one which has the same major version as the package version
- flex executes an "auto-generated" recipe for any package which has type `symfony-bundle` and no flex recipe defined - this recipe simply registers
the bundle in bundles.php
- it's possible to set an alias for a package. For example running `composer require doctrine` is equivalent to running
`composer require symfony/orm-pack`
- flex has some integration with docker, see the [docs](https://symfony.com/doc/current/setup/docker.html#flex-recipes-docker-configuration)

The recipe can define the content of config files which should be created on installation so these configs don't have to
live in the package repository. It's also possible to define which files should be copied from the package into the 
project during installation. If the recipe manifest file defines a bundle, assets from the package's `/public` directory 
will be symlinked in the project's `/public` directory, so there's no need to explicitly copy these.
> There is currently a bug which causes recipe unconfiguration to fail if there were files copied from the package -
> until this is fixed, make sure to remove any such files before you run `composer remove {package}`. If you fail to do 
> this and you see an error, you might have to remove the recipe from symfony.lock to make sure it's properly uninstalled

### Commands

- To install a package along with its recipe, run `composer require {package}` as you normally would.
- To check what recipes are installed, run `composer recipes`
- It is possible to request recipe installation separately from a composer package installation by running 
`composer recipes:install tpoint/tpoint-plugin-example --force -v`

If you run into issues installing/reinstalling a recipe, check if the recipe is already in symfony.lock. Removing it 
will force flex to do a fresh installation.

### Custom recipes

In the past there was an official Symfony Flex server and it was only possible to use recipes hosted there. This has 
changed and it's now possible for recipes to be hosted in any git repository. This means it's also possible to set up a
custom (private) flex repository, as we have done [here](https://github.com/samm-tpoint/flex). There is some extra 
configuration required in composer.json to be able to use a custom recipe repository (see step 2 of installation below)


For more information, see the [official documentation](https://symfony.com/doc/5.4/setup/flex_private_recipes.html) on private recipes.
