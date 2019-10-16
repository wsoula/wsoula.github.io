Amazon CDK
---
I discovered the Amazon CDK recently and am pretty impressed by it.  It uses cloudformation under the covers
but gives you the ability to do the deploy in your terminal and then see the cloudformation output in your
terminal.  You can even do a diff to see what will change.

Two things I found to be difficult with the CDK was to add a Search to a graph widget so I could show
per instance metrics and adding an sns topic to an alarm.

SNS Topic
---
The sns topic was surprisingly difficult to figure out even though all you really need to do is provide a topics
arn.  It took two links before everything clicked and I was able to get it working correctly:
https://github.com/aws/aws-cdk/issues/2089#issuecomment-531350352 and https://forums.aws.amazon.com/thread.jspa?messageID=916931

```
criticalSnsTopic = sns.Topic.from_topic_arn(self,'criticalTopic',snsCriticalArn)
criticalSnsAction=cloudwatch_actions.SnsAction(snsCriticalTopic)
alarm.add_alarm_action(criticalSnsAction)
```

Search in Graph
---
I found this link:
https://github.com/aws/aws-cdk/issues/1077#issuecomment-529689658
about using metric math in an alarm but the idea of using the escape hatches gave me hope I could do the same
for the Search, since it is just metric math.  The problem was that Alarms cannot have a search that returns multiple values,
even if you edit it in the UI.  In the end I had to send all my widgets `.to_json()[1:][:-1]` and build up my dashboard body
in json.  But I am able to display per instance metrics without using a lambda
