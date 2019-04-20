# Pootle Instance For KDE

Trying to integrate [KDE translation](https://l10n.kde.org) with Pootle for easy translation.

## Translation

* Create an account
* [Choose a file](http://kde.anishsheela.com/ml/l10n-kf5/)
* Start translating. [Please check the priority list](#priority-list)

**IMPORTANT** : There maybe a `Your names` (`NAME OF TRANSLATORS`) and email to `Your emails` (`EMAIL OF TRANSLATORS`) string in the file. Add your name and email to it. This value gets displayed in program's **About** menu. If there already exist a value, add yours after a comma.

The `Your names` and `Your emails` strings will only exist in some files. If it exist, do add your name to it !

Example :

* Your names :
  ```
  ശ്യാം കൃഷ്ണന്‍ സി.ആര്‍.,സുബിന്‍ സിബി
  ```
* Your emails :
  ```
  shyam@example.com,subin@example.com
  ```

### Priority

Consider translating these files :

* Files having `dolphin` in it :
  * [applications/dolphin._desktop_.po ](http://kde.anishsheela.com/ml/l10n-kf5/applications/dolphin._desktop_.po)
  * [applications/dolphin.po](http://kde.anishsheela.com/ml/l10n-kf5/applications/dolphin.po)
  * [applications/org.kde.dolphin.appdata.po](http://kde.anishsheela.com/ml/l10n-kf5/applications/org.kde.dolphin.appdata.po)
* Files having `plasma` in it :
  * [kde-workspace/plasma-workspace._desktop_.po](http://kde.anishsheela.com/ml/l10n-kf5/kde-workspace/plasma-workspace._desktop_.po)
  * [kde-workspace/plasma-discover](http://kde.anishsheela.com/ml/l10n-kf5/kde-workspace/plasma-discover.po)
* Files having `kwin` in it :
  * [kde-workspace/kwin.po](http://kde.anishsheela.com/ml/l10n-kf5/kde-workspace/kwin.po)

Choose files based on the activity.

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
* Extra : [set up elasticsearch for translation memory](http://docs.translatehouse.org/projects/pootle/en/stable-2.8.x/features/translation_memory.html)

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