I was tasked at work with setting up Amazon's MSK and we wanted to set
`allow.everyone.if.no.acl.found` to false and I saw it is part of the default
cofiguration for MSK.  https://docs.aws.amazon.com/msk/latest/developerguide/msk-default-configuration.html

But when I tried to set that property in a custom configuration, I would get the error:
"Unsupported config allow.everyone.if.no.acl.found for Kafka Version 2.2.1".  I opened
a support ticket with Amazon and this is the response:
> I discussed with msk team regarding  your query and as of now, this configuration parameter
> cannot be set which means your cluster will have default value(true) allowing everyone to
> access the resource, not just the super users.

So there you have it.  As of right now you cannot set that property in Amazon MSK.  But you can
immediately setup acls for a topic and keep it secure.
