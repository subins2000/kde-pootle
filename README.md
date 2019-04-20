# Pootle Instance For KDE

Trying to integrate [KDE translation](https://l10n.kde.org) to Pootle for easy translation.

## Setup

* Install [pootle](http://docs.translatehouse.org/projects/pootle/en/stable-2.8.x/server/installation.html)

  This repo use `pipenv`
  ```
  export PIPENV_VENV_IN_PROJECT=1
  pipenv install
  ```
* Link `pootle.conf` :
  ```
  pootle init
  ln -s $(realpath pootle.conf) ~/.pootle/pootle.conf
  ```
* `pootle initdb --no-projects`
* Make the `translations` folder.
  ```
  mkdir translations
  mkdir translations/l10n-kf5
  mkdir translations/l10n-kf5/templates/
  ln -s $(realpath translations) .venv/lib/python2.7/site-packages/pootle/translations
  ln -s $(realpath log) .venv/lib/python2.7/site-packages/pootle/log
  ```

## Running

Get into the virtual environment shell and run `pootle runserver --insecure`

or use the `start.sh` script to run in background :

```
nohup bash start.sh > foo.out 2> foo.err < /dev/null &
```

Use `pkill -f pootle` to kill the process.

## Adding Project To Pootle

KDE's l10n repository is using Subversion. A [mirror of it under `git`](https://github.com/FOSSersVAST/kde-ml) is used by us. Do the following process in that repo.

### The Mirror git repo

Find the package to add from [here](https://l10n.kde.org/stats/gui/trunk-kf5/package/). Example : `applications`, `kde-workspace`

* Set the package :
  ```
  export REPO_ROOT=$PWD
  export PACKAGE=''
  ```
* Get the `template` files :

  ```
  cd $REPO_ROOT/translations/l10n-kf5/templates
  svn checkout svn://anonsvn.kde.org/home/kde/trunk/l10n-kf5/templates/messages/$PACKAGE $PACKAGE
  ```
* Get the language files :

  ```
  export LANG_CODE='ml'
  mkdir $REPO_ROOT/translations/l10n-kf5/$LANG_CODE
  cd $REPO_ROOT/translations/l10n-kf5/$LANG_CODE
  svn checkout svn://anonsvn.kde.org/home/kde/trunk/l10n-kf5/$LANG_CODE/messages/$PACKAGE $PACKAGE
  ```

### Pootle

* [Setup a SSH key](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key) and add it as [deploy key](https://developer.github.com/v3/guides/managing-deploy-keys) to the mirror git repo
* Clone the mirror repo to Pootle base directory (this repo base) :
  ```
  git clone git@github.com:FOSSersVAST/kde-ml.git translations
  ```
* Open Pootle admin and [add project](http://docs.translatehouse.org/projects/pootle/en/stable-2.8.x/server/project_setup.html)

  The following project variables need to be changed :

  * Code : `l10n-kf5`
  * Full Name : `KDE 5 Trunk`
  * File types : `Gettext PO`
  * Path or URL : `{POOTLE_TRANSLATION_DIRECTORY}l10n-kf5`
  * Path mapping preset : `non-GNU style`
  * Template name : `templates`

  The extra options are only in Pootle 2.9

* [Sync Pootle](http://docs.translatehouse.org/projects/pootle/en/stable-2.8.x/features/using_pootle_fs.html) :
  ```
  pootle fs add l10n-kf5
  pootle fs fetch l10n-kf5
  pootle fs sync l10n-kf5
  ```