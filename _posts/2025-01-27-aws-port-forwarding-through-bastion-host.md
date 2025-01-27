I was tasked with running a long sql command and then outputting that into a CSV.  This went great
on every RDS instance but one, which I could not connect to.  The bastion host, though, was able
to connect to it.  The problem was that I wouldn't be able to get a CSV using just the mysql
command line.  I had to install MySQL Workbench locally to get a proper CSV, especially since the
data had commas in it.  I had already tried multiple things to do it with just mysql command line
and I was not satisfied with the results and that all multiple thousand rows would be correct.

So I spent several hours trying to get port forwarding to work on the bastion host where I could
connect to it on port 3306 and then have the bastion connect to the RDS on 3306 so  could use
MySQL Workbench.  I could not get it working even though I had the exact same thing on a
different bastion in a different region, although that one had a public IP, which might have
been the difference.

I finally found that the aws cli and the session manager plugin will do exactly this with no
trouble at all.  I ran that command and was instantly able to connect with MySQL Workbench
and run the script and get the CSV I needed.  Because I run the aws cli as a docker container
and I haven't found a way to run that with the session manager plugin I had to use a local
install of the aws cli and session manager plugin.  The command I ran was:
`/usr/local/aws-cli/aws ssm start-session --target <bastion instance id> --document-name AWS-StartPortForwardingSessionToRemoteHost --parameters '{"portNumber":["3306"], "localPortNumber":["3306"]}' --profile <profile> --region <region> --parameters host="<RDS Host>",portNumber="3306",localPortNumber="3306"`

Then just connect to 127.0.0.1:3306 with MySQL Workbench and you are connected to the RDS you
couldn't connect to before.

Source: https://aws.amazon.com/blogs/database/securely-connect-to-an-amazon-rds-or-amazon-ec2-database-instance-remotely-with-your-preferred-gui/
