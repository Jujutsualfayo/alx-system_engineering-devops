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
Then the gods of code came through( capital G btw sorry) and i decided to ping the database server to ensure that it is actually reacheable over a network setup. then checked the rules of the firewall (which are many btw but necessary) to see if any were blocking by running sudo iptables -L -n | grep <port>
and then yes. i found the issue glaring on my screen telnet: Unable to connect to remote host: No route to host
and then i quickly ran sudo iptables -A INPUT -p tcp --dport <port> -j ACCEPT
sudo iptables-save
to allow connection. 
Tada!`

## Summation

I ended up finding the root cause of the issue as a port misconfig which was not able to connect to a listening network setup/
## Prevention

Test the application before deploying. This error would have arisen
and could have been addressed earlier had the app been tested.

 Status monitoring. Enable some uptime-monitoring service such as
[UptimeRobot](./https://uptimerobot.com/) to alert instantly upon outage of the website.


