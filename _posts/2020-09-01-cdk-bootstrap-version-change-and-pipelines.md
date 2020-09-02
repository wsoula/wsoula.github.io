CDK Bootstrap Version 4 and Pipelines
---

I had a working cdk pipeline that I created shortly after they released the pipeline work.
This was working on Wednesday and then I went on vacation and the weekend and on Monday it no
longer worked and the 'update pipeline' phase was failing with this error:

```
Error: cdk-pa-sysops-event-store-pipeline: publishing assets requires bootstrap stack version '4', found '3'. Please run 'cdk bootstrap' with a newer CLI version.
```

I then went and bootstrapped again and that didn't fix it and that was when I realized that a new
version of the cdk was released.  So I updated my cdk version and then was able to bootstrap again and
the error in the 'update pipeline' went away and I had a new error.  The 'asset' phase was had this error:

```
error  : [100%] fail: Missing credentials in config, if using AWS_CONFIG_FILE, set AWS_SDK_LOAD_CONFIG=1
Failure: CredentialsError: Missing credentials in config, if using AWS_CONFIG_FILE, set AWS_SDK_LOAD_CONFIG=1
```

It took awhile to figure out but I discovered that I was bootstrapping like this

```
cdk bootstrap --profile saml --cloudformation-execution-policies arn:aws:iam::aws:policy/AdministratorAccess aws://<resource_account_id>/us-east-1
```

And since my pipelines are in one account and the resources are in a different account I needed to add the trust from the pipeline
account to the resource accounts when I was bootstrapping.  So then when I bootstrapped with the trust option my pipeline worked
again.

```
cdk bootstrap --profile saml --cloudformation-execution-policies arn:aws:iam::aws:policy/AdministratorAccess --trust <pipeline_account_id> aws://<resource_account_id>/us-east-1
```

<iframe src="https://api.countapi.xyz/hit/wsoula/blog_20200901" width="150" height="50"/>
