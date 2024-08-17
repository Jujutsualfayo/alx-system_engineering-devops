# Postmortem of InteriorHealth APP WEB INFRASTRUCTURE.

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
Then the gods of code came through( capital G btw sorry) and i decided to ping the database server to ensure that it is actually reacheable over a network setup. And there i found the issue. Displaying an error message telnet: Unable to connect to remote host: No route to host
.

23:26 UTC
Quickly i ran sudo iptables -L -n | grep <port> to check if any firewalls were getting in the way. Then sudo iptables -A INPUT -p tcp --dport <port> -j ACCEPT
sudo iptables-save
to allow any blocked ports causing the  issue.
And i finally got the server to accept connections.

## Summation

After examining the patient. The issues was diagnosed as a misconfig where the port handling transaction did not have listening network
## Prevention

Test thoroughly  the application before deploying. This error would have arisen
and could have been addressed earlier had the app been tested.

 Status monitoring. Enable some uptime-monitoring service such as
[UptimeRobot](./https://uptimerobot.com/) to alert instantly upon outage of the website.

And that friends is how Benj Amin (Haha i meant it) helped solved the puzzle!!
