Restoring an RDS in Another Account
---

I had a need to restore an RDS from one AWS account to another and I found these handy instructions to follow:
https://aws.amazon.com/premiumsupport/knowledge-center/rds-snapshots-share-account/  So I started down this path
and the first thing I ran into is I used the default kms keys and so the snapshot was not shareable.  I then shared
with a customer created KMS key.  Now I had to find out how to share that key with the other account as well.  I found
this documentation for that as well:
https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying-external-accounts.html  I only did the part
where I put the root user of the account I wanted to share to on the key policy.  I was using the CDK so I ran my code
and it failed saying:
`The specified KMS key [BLAH] does not exist, is not enabled or you do not have permissions to access it. (Service: AmazonRDS; Status Code: 400; Error Code: KMSKeyNotAccessibleFault`

This error made me think it was a permission issue but on the role executing the CDK code.  So I manually added a
policy permission to the executing role and that didn't work.  I kept adding it to all the CDK roles and got the
same error.  I even tried to manually restore it and got the same error.

Now I wanted to make sure I even had access to the key from the other account so I went to the aws cli and ran:
`aws kms describe-key --key-id BLAH --profile OTHER_ACCOUNT` and I was able to describe it so I new I had access
and I had access without modifying the role my user was attached to, so the troubleshooting above was in vain.

After some googling and thinking about the issue I wondered if the permissions were just wrong in the AWs documentation.
I found this stackoverflow article that mentioned needing `kms:CreateGrant`:
https://stackoverflow.com/questions/45821144/minimal-kms-permissions-to-copy-a-database-snapshot

So at the end of the day to share the KMS key so I could restore the RDS snapshot I had to put these permissions
on the KMS key policy and that was it:
```
{
    "Sid": "Manually added for OTHER_ACCOUNT to use this key to decrypt an rds snapshot",
    "Effect": "Allow",
    "Principal": {
        "AWS": "arn:aws:iam::OTHER_ACCOUNT:root"
    },
    "Action": [
        "kms:Decrypt",
        "kms:DescribeKey",
        "kms:Encrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:CreateGrant"
    ],
    "Resource": "*"
}
```
