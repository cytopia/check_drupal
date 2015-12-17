# check_drupal
Nagios drupal plugin to monitor the state of a drupal site for security updates, system updates, core errors, core warnings and missing db updates.

[![Build Status](https://travis-ci.org/cytopia/check_drupal.svg?branch=master)](https://travis-ci.org/cytopia/check_drupal)
 [![Latest Stable Version](https://poser.pugx.org/cytopia/check_drupal/v/stable)](https://packagist.org/packages/cytopia/check_drupal) [![Total Downloads](https://poser.pugx.org/cytopia/check_drupal/downloads)](https://packagist.org/packages/cytopia/check_drupal) [![Latest Unstable Version](https://poser.pugx.org/cytopia/check_drupal/v/unstable)](https://packagist.org/packages/cytopia/check_drupal) [![License](https://poser.pugx.org/cytopia/check_drupal/license)](http://opensource.org/licenses/MIT)
 [![POSIX](https://img.shields.io/badge/posix-100%25-brightgreen.svg)](https://en.wikipedia.org/?title=POSIX)
 [![Type](https://img.shields.io/badge/type-%2Fbin%2Fsh-red.svg)](https://en.wikipedia.org/?title=Bourne_shell)

---

* `check_drupal` on [Icinga Exchange](https://exchange.icinga.org/cytopia/check_drupal)
* `check_drupal` on [Nagios Exchange](https://exchange.nagios.org/directory/Plugins/CMS-and-Blog-Software/Drupal/check-2Ddrupal/details)

---

##### NOTE
This check can be used in two ways:

1. Let nagios always trigger `check_drupal` which might take 1-3 seconds and cause some load
2. Let nagios simply parse the logfile (with `check_drupal_log`) created by `check_drupal` via cron on the target machine.

I would recommend the second option as you do not check each drupal site every 5 minutes and also in order to keep the nagios check as fast as possible. For that use cron to trigger the `check_drupal` on the target machine every 6 hours or so.


##### Requirements
| Program  | Required | Description |
| ------------- | ------------- | -------- |
| bourne shell (sh)  | yes  | The whole script is written in pure bourne shell (sh) and is 100% Posix compliant |
| [check_by_ssh](https://www.monitoring-plugins.org/doc/man/check_by_ssh.html)  | yes  | This nagios plugin is used as a wrapper to check on remote hosts |
| [drush](http://www.drush.org) | yes  | This nagios plugin requires drush to be installed on the target machine |

##### Features
* Check for Drupal security updates
* Check for Drupal system updates
* Check for Drupal required database updates
* Check for Drupal core errors
* Check for Drupal core warnings
* Every check can specify its own nagios severity (Error or Warning)
* Custom name for nagios short output
* Detailed information in nagios long output
* Be able to successfully recognize valid Drupal6 or Drupal7 document root
* Basic performance data fow: how many OKs, Errors, Warnings and Unknowns



## 1. Usage

### 1.1 `check_drupal`

With `-l` you will be able to run the `check_drupal` locally on each machine only a few times a day and dump the output to a logfile.
This file can then be checked normaly via nagios by calling `check_drupal_log` instead, which will just read the log and not put any load onto the machine.
Multiple logfiles for multiple drupal site per server will be possible.

```shell
Usage: check_drupal -d <drupal root> [-n <name>] [-s <w|e>] [-u <w|e>] [-e <w|e>] [-w <w|e>] [-m <w|e>] [-l <log file>]
OR     check_drupal --check
OR     check_drupal --help
OR     check_drupal --version

Nagios plugin that will check drupal sites for errors.
Errors include the following: available security updates,
missed database updates and drupal status errors.
For each check you can specify the nagios severity (error or warning).

  -d <drupal root>       The full path to the drupal document root (usually
                         the 'drupal' folder. This parameter is required.

  -n <name>              [optional] Specify a name for the drupal instance to
                         appear on the nagios output. The default is 'Drupal'

                         the 'drupal' folder. This parameter is required.

  -s <w|e>               [optional] Check for drupal core and module security
                         updates and return nagios error or warning.
                         Warning:  -s w
                         Error:    -s e

  -u <w|e>               [optional] Check for drupal core and module updates
                         in general and return nagios error or warning.
                         Warning:  -u w
                         Error:    -u e

  -e <w|e>               [optional] Check for drupal status errors and return
                         nagios error or warning.
                         Warning:  -e w
                         Error:    -e e

  -w <w|e>               [optional] Check for drupal status warnings and return
                         nagios error or warning.
                         Warning:  -w w
                         Error:    -w e

  -m <w|e>               [optional] Check for drupal missed database updates
                         and return nagios error or warning. (They can occur
                         when you update core or modules and forget the db).
                         Warning:  -m w
                         Error:    -m e

  -l <log file>          [optional] Instead of checking all of the above via nagios
                         every five minutes or so, run this script via cron once a day
                         (or twice) and write the output into a logfile. This logfile can
                         then be checked by the nagios plugin 'check_drupal_log' which is
                         less costy in terms of load/cpu.
                         See 'check_drupal_log --help' for more info.
                         Example:
                         check_drupal -d /var/www -s e -e e -w w -l /var/log/drupal.log
                         check_drupal_log -l /var/log/drupal.log

  --check                Check for program requirements.
  --help                 Show this help
  --version              Show version information.
```

### 1.2 `check_drupal_log`

```bash
Usage: check_drupal_log -f <logfile>
OR     check_drupal_log --help
OR     check_drupal_log --version

Nagios plugin that will parse the logfile created by 'check_drupal'.

  -f <logfile>           The full path to logfile created by 'check_drupal'

  --help                 Show this help
  --version              Show version information.
```

## 2. Examples

The following examples are run directly from the command line. The exit code will always be aggregated, meaning if the program throws a warning and an error, the final exit code will result in an error.

Also to note: The first line until the `|` represents the actual nagios output. Everything in the first line behind the `|` is performance data used to generate the cool charts. Everything from line two onwards is nagios extended status info (when you click on details).

**Check for security updates**
```bash
./check_drupal -d /shared/httpd/sites-drupal/COOL-PROJECT/drupal/ -n COOL-PROJECT -s e
[ERROR] COOL-PROJECT has errors: Security update(s) | 'OK'=0;;;0;1 'Errors'=1;1;1;0;1 'Warnings'=0;1;;0;1 'Unknown'=0;;;0;1
==== SECURITY UPDATES ====
[CRITICAL] drupal 7.40 -> 7.41
[CRITICAL] jquery_update 7.x-2.6 -> 7.x-2.7
```

**Check for security and normal updates**
```bash
./check_drupal -d /shared/httpd/sites-drupal/COOL-PROJECT/drupal/ -n COOL-PROJECT -s e -u w
[ERROR] COOL-PROJECT has errors: Security update(s), Update(s) | 'OK'=0;;;0;2 'Errors'=1;1;1;0;2 'Warnings'=1;1;;0;2 'Unknown'=0;;;0;2
==== SECURITY UPDATES ====
[CRITICAL] drupal 7.40 -> 7.41
[CRITICAL] jquery_update 7.x-2.6 -> 7.x-2.7
==== SYSTEM UPDATES ====
[WARNING] field_collection 7.x-1.0-beta9 -> 7.x-1.0-beta10
[WARNING] views 7.x-3.11 -> 7.x-3.13
[WARNING] bootstrap 7.x-3.0 -> 7.x-3.1
```

**Check for all possible stuff**
```bash
./check_drupal -d /shared/httpd/sites-drupal/COOL-PROJECT/drupal/ -n COOL-PROJECT -s e -u w -e e -w w -m e
[ERROR] COOL-PROJECT has errors: Security update(s), Update(s), Core error(s), Core warning(s) | 'OK'=1;;;0;5 'Errors'=2;1;1;0;5 'Warnings'=2;1;;0;5 'Unknown'=0;;;0;5
==== SECURITY UPDATES ====
[CRITICAL] drupal 7.40 -> 7.41
[CRITICAL] jquery_update 7.x-2.6 -> 7.x-2.7
==== SYSTEM UPDATES ====
[WARNING] field_collection 7.x-1.0-beta9 -> 7.x-1.0-beta10
[WARNING] views 7.x-3.11 -> 7.x-3.13
[WARNING] bootstrap 7.x-3.0 -> 7.x-3.1
==== CORE ERRORS ====
[CRITICAL] CKeditor
[CRITICAL] Cron-Wartungsaufgaben
[CRITICAL] CTools CSS Cache
[CRITICAL] Aktualisierungsstatus von Modulen und Themes
[CRITICAL] Aktualisierungsstatus des Drupal-Kern
==== CORE WARNINGS ====
[WARNING] Token
==== DB UPDATES ====
[OK] No database updates required
```
**Check for db updates**
```bash
./check_drupal -d /shared/httpd/sites-drupal/COOL-PROJECT/drupal/ -n COOL-PROJECT -m e
[OK] COOL-PROJECT is healthy | 'OK'=1;;;0;1 'Errors'=0;1;1;0;1 'Warnings'=0;1;;0;1 'Unknown'=0;;;0;1
==== DB UPDATES ====
[OK] No database updates required
```

## 3. Nagios Configuration

### 3.1 check_drupal

#### Command definition
In order to check drupal sites on remote servers you will need to make use of `check_by_ssh`.
```bash
name:    check_by_ssh_drupal
command: $USER1$/check_by_ssh -H $HOSTADDRESS$ -t 60 -l "$USER17$" -C "$USER22$/check_drupal -d $ARG1$ -n $ARG2$ $ARG3$"
```
#### Service definition
In the above command definition there are two fixed arguments for the document root and the project name as well as one loose argument place holder that can hold all checks you want to run. The following shows one example service definition for one specific drupal site:
```bash
check command: ssh_drupal_cool-drupal-project
$ARG1$:        /var/www/cool-drupal-project/drupal/
$ARG2$:        Cool Drupal Project
$ARG3$:        -s e -u w -e e -w w -m e
```
The above service defintion will check against security updates (with nagios error), against normal updates (with nagios warning), against core errors (with nagios error), against core warnings (with nagios warning) and finally against missed database updates (with nagios error).

### 3.2 check_drupal_log

#### Command definition
In order to check drupal sites on remote servers you will need to make use of `check_by_ssh`.
```bash
name:    check_by_ssh_drupal
command: $USER1$/check_by_ssh -H $HOSTADDRESS$ -t 60 -l "$USER17$" -C "$USER22$/check_drupal_log -f $ARG1$"
```
#### Service definition
In the above command definition there is only one arguments. This will point to the logfile created by `check_drupal`:
```bash
check command: ssh_drupal_cool-drupal-project
$ARG1$:        /var/log/drupal_cool-project.log
```

#### Cron setup
For this recommended setup to work you need to setup a cronjob on the target machine (where the drupal site is installed) that is run every 6 hours, every day or whatever you want.

Setup multiple cronjobs with multiple logfiles if you have multiple drupal sites on this machine that you want to monitor.

```cron
0 */6 * * * /path/to/check_drupal -d /var/www/cool-drupal-project/drupal/ -n "Cool Project" -s e -u w -e e -w w -m e -l /var/log/drupal_cool-project.log
```




## 4. License
[![license](https://poser.pugx.org/cytopia/check_drupal/license)](http://opensource.org/licenses/mit)

## 5. Awesome

Added by the following [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome) lists:

* [awesome-nagios-plugins](https://github.com/cytopia/awesome-nagios-plugins)
