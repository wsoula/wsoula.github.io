I've been using the aws docker container for some time now and recently had the use case where I needed to grep
the output to use in further commands.  I ran into an issue where there would be random "ungreppable" characters
in the output that would cause the full value to not get pulled out.  Things like `^H` and the like.  After much
searching I finally found this github issue: https://github.com/aws/aws-cli/issues/5455 and looking through the
comments I found one that towards the bottom that seemed exaclty like what I was running into:
https://github.com/aws/aws-cli/issues/5455#issuecomment-854081126.  So I removed the `-it` from my aws alias and
now everything is working perfectly.  Such a simple solution to a hair pulling problem introduced by using
docker instead of aws.
