I have known about crossplane for a long time now and it seemed interesting but in an environment where I wasn't using
k8s it seemed like overkill to run k8s just to provision infrastructure.  But with the recent move from ECS to EKS I was
able to start entertaining the idea of using crossplane.  The problem was I didn't want to introduce yet another
infrastructure pattern on top of our existing terraform flow.  Then I was recently put on a big project that was one of
the top two priorities for the company.  This new project had a lot of AWS resources involved as well as a new lambda
to kick everything off.  My first take was to stop using lambda and just run the container in k8s using cron.  This
allowed me to use our existing automation, with some tweaks, for microservices and getting their image tag.  The
developer had created all the resources with terraform and AI.  This means I would have to translate the AI terraform
into something that would be reusable and fit in our existing terraform ecosystem.  I was talking to a friend of mine
this summer and he said his dev teams where allowed to even write their own charts for deploy if they wanted and it
made me want to implement something like that where I am, but I didn't see the need for it yet.  I was starting to have
an idea form in my head of allowing the developer to manage their own infrastructure.  I asked the developer to create
the resources in crossplane instead of terraform and the AI produced working code.  I looked at it and saw some areas
for improvement the biggest being that there was a lot of code around building arns because some of the crossplane
resources do not allow you to reference other resources by name.  I did not like this approach and though there must be
a better way.  Through some researching I discovered composite resources and creating a composition in pipeline mode.
Using this I could use crossplane's templating to only create resources when their dependent resources were created and
had their attributes available.  This means I can get any resources arn and know that resource is created and ready.
Now if names change format the code doesn't break because it is looking at the object and its attributes in k8s rather
than blindly building arns.  At this point I had a chart that created all my resources in aws and I needed it to deploy
alongside deploying the code.  I added a simple check for a helmfil.yaml.gotmpl in the deployment automation and if
that is found it runs a simple helmfile apply -f helmfile.yaml.gotmpl --environment {environment} command before
deplopying the code.  This means I have finally reached the holy grail of empowring developers.  Now all the config and
infrastructure and code for a service all live together and the people that know the most about the code and what it is
trying to do have full power to tune their infrastructure to meet it.  Once code is ready to move out of dev it is
tagged and that tag includes everything about the service and moves through the rest of the environment to production.
