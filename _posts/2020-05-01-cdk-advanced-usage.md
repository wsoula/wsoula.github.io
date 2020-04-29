
One of the nice things about the CDK is that it is just a programming language
under the covers.  This means that you can use any additional packages you
want.  We had a case where the product in question wasn't going to be worked
on and we had to use instance ids to get some metrics.  I had the idea to import
the boto3 library and then do a search in the cdk code to get the instance ids
to populate my alarms.  I already had a code pipeline for CICD so I created a
cloudwatch event that triggers on insufficient data that kicks off the CICD
pipeline to re-populate the alarms with the new instance ids.  We recycle instances
so doing it by instance id was less than ideal but necessary in this case.

In the below snippet, the cicd pipeline is stored earlier in the variable `cicd_pipeline`.
The list of alarms that can trigger this even are in the `alarm_arns` variable which is
built earlier from the search for instances.
```
pipeline_target=targets.CodePipeline(pipeline=cicd_pipeline)
events.Rule(self,'event1',description='Event triggered by insufficient data and triggers the cicd pipeline to update the instance ids due to recycles',
            event_pattern=events.EventPattern(resources=alarm_arns,detail_type=["CloudWatch Alarm State Change"],
                                              source=['aws.cloudwatch']),targets=[pipeline_target])
```
