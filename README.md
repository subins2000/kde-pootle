# Pootle Instance For KDE

Trying to integrate [KDE translation](https://l10n.kde.org) to Pootle for easy translation.

## Setup

* Install [pootle](http://docs.translatehouse.org/projects/pootle/en/stable-2.8.x/server/installation.html)

  This repo use `pipenv`
* 
* Download translations to the `translation` folder. Example :
  ```
  mkdir l10n-kf5
  cd l10n-kf5
  svn checkout svn://anonsvn.kde.org/home/kde/trunk/l10n-kf5/templates/messages templates
  ```


## Running

Get into the virtual environment shell and run `pootle runserver --insecure`