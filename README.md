# check_drupal
Nagios drupal plugin to monitor the state of a drupal site via ssh, http or locally

[![Build Status](https://travis-ci.org/cytopia/check_drupal.svg?branch=master)](https://travis-ci.org/cytopia/check_drupal)
 [![Latest Stable Version](https://poser.pugx.org/cytopia/check_drupal/v/stable)](https://packagist.org/packages/cytopia/check_p hp) [![Total Downloads](https://poser.pugx.org/cytopia/check_drupal/downloads)](https://packagist.org/packages/cytopia/check_ph p) [![Latest Unstable Version](https://poser.pugx.org/cytopia/check_drupal/v/unstable)](https://packagist.org/packages/cytopia/ check_drupal) [![License](https://poser.pugx.org/cytopia/check_drupal/license)](http://opensource.org/licenses/MIT)
 [![POSIX](https://img.shields.io/badge/posix-100%25-brightgreen.svg)](https://en.wikipedia.org/?title=POSIX)
 [![Type](https://img.shields.io/badge/type-%2Fbin%2Fsh-red.svg)](https://en.wikipedia.org/?title=Bourne_shell)


## Usage
```bash
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
