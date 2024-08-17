# Postmortem of InteriorHealth app containers

Upon the release of our project dubbed InteriorHealth.llc for Research & project approval,
at precisely 22:00 coordinated universal time (UTC), an outage occurred on an isolated
Oracle Database which we manage through the SQL*Plus. GET requests on the server led to
`Transaction timeout. The operation could not be completed within the allotted time, when the expected response was an HTML file defining the successful operand to have been completed.

## Debugging Process

Chief  engineer  Benj amin (Trust me the word sequence is not a typo i thrive with ununiformity sigh) encountered the issue upon opening the project and being, well, instructed to
address it, roughly 23:02 UTC.Issue detected by multiple TEST users who reported an inability to complete their transactions.  He promptly proceeded to undergo solving the problem.

23:10 UTC.
 Checked running processes using `ps aux`. Two `apache2` processes - `root` and `www-data` -
were properly running.

23:14 UTC.
 Looked in the `sites-available` folder of the `/etc/apache2/` directory. Determined that
the web server was serving content located in `/var/www/html/`.

23:19 UTC
 In one terminal, ran `strace` on the PID of the `root` Apache process. In another, curled
the server. Expected great things... only to be disappointed. `strace` gave no useful
information.

23:22 utc
Then the gods of code came through( capital G btw sorry) and i decided to ping the database server to ensure that it is actually reacheable over a netwrk setup. then checked the 

## Summation

In short, a typo. Gotta love'em. In full, the WordPress app was encountering a critical
error in `wp-settings.php` when tyring to load the file `class-wp-locale.phpp`. The correct
file name, located in the `wp-content` directory of the application folder, was
`class-wp-locale.php`.

Patch involved a simple fix on the typo, removing the trailing `p`.

## Prevention

This outage was not a web server error, but an application error. To prevent such outages
moving forward, please keep the following in mind.

* Test! Test test test. Test the application before deploying. This error would have arisen
and could have been addressed earlier had the app been tested.

* Status monitoring. Enable some uptime-monitoring service such as
[UptimeRobot](./https://uptimerobot.com/) to alert instantly upon outage of the website.

Note that in response to this error, I wrote a Puppet manifest
[0-strace_is_your_friend.pp](https://github.com/bdbaraban/holberton-system_engineering-devops/blob/master/0x17-web_stack_debugging_3/0-strace_is_your_friend.pp)
to automate fixing of any such identitical errors should they occur in the future. The manifest
replaces any `phpp` extensions in the file `/var/www/html/wp-settings.php` with `php`.

But of course, it will never occur again, because we're programmers, and we never make
errors! :wink:
