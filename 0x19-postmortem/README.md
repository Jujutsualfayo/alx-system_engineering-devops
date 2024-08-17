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
Then the gods of code came through( capital G btw sorry) and i decided to ping the database server to ensure that it is actually reacheable over a netwrk setup. then checked the groups config to allow traffic on the database port. Whilst testing the ports connectivity if by any chance it allows connections and is open i found the bug. Right there and then it read telnet: Unable to connect to remote host: Connection timed out

## Summation

In short, a misconfig. There was no service listening on the port.

## Prevention

Load Balancing:
 Implement more robust load balancing mechanisms to better distribute traffic across the sandbox environment.

Resource Allocation:
 Increase server capacity and allocate additional resources to handle peak usage times.
Monitoring: Enhance monitoring systems to detect and alert on potential overloads before they cause system downtime.

User Education: Provide users with alternative methods (e.g., SSH access via external terminals) as standard practice during outages.

 Test the application before deploying. This error would have arisen
and could have been addressed earlier had the app been tested.

 Status monitoring. Enable some uptime-monitoring service such as
[UptimeRobot](./https://uptimerobot.com/) to alert instantly upon outage of the website

And that is how Chief debugking Benj Amin saved the world.

