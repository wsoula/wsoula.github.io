CI/CD for AWS CDK
---
I've written before about the AWS CDK but today I am writing about my experiences creating a CI/CD pipeline for my CDK work.
The first thing I came across was that my code needed to be in github instead of our private bitbucket to make this work
easier.  Below is the code for my pipeline where I can just pass in the org, repo, stack name, and oauth secret arn.
Walking through it the first thing I do is setup getting the oauth token for the read only user in our github org.  Then
I create the pipeline project and add the ability for its role to get the secret value.  After that its just creating the
three actions: get the github source, run the build, update the cloudformation stack.

```
from aws_cdk import (
    core,
    aws_codepipeline as codepipeline,
    aws_codepipeline_actions as codepipeline_actions,
    aws_codebuild as codebuild,
    aws_cloudformation as cloudformation,
    aws_iam as iam,
    aws_s3 as s3,
    aws_secretsmanager as secretsmanager
)

class Cicd(core.Construct):
    def __init__(self, scope: core.Construct, id: str, **kwargs) -> None:
        super().__init__(scope, id, **kwargs)

    def pipeline(self,owner,repo,stack_name,oauth_token_arn,branch='master'):
        oauth = secretsmanager.Secret.from_secret_arn(self,repo+'-secret',secret_arn=oauth_token_arn)
        oauth_token = oauth.secret_value
        project = codebuild.PipelineProject(self,repo,build_spec=codebuild.BuildSpec.from_source_filename('buildspec.yaml'),project_name=repo,
                                                environment=codebuild.BuildEnvironment(privileged=True))
        project.add_to_role_policy(iam.PolicyStatement(actions=['secretsmanager:GetSecretValue'],resources=[oauth_token_arn]))
        github_source = codepipeline.Artifact('github-source')
        deploy_artifacts = codepipeline.Artifact('cfn_templates')
        github_source_action = codepipeline_actions.GitHubSourceAction(oauth_token=oauth_token,output=github_source,owner=owner,repo=repo,branch=branch,action_name='clone')
        code_build_action = codepipeline_actions.CodeBuildAction(action_name='build',input=github_source,outputs=[deploy_artifacts],project=project)
        update_stack_action = codepipeline_actions.CloudFormationCreateUpdateStackAction(action_name='deploy',template_path=deploy_artifacts.at_path('cdk.out/'+stack_name+'.template.json'),admin_permissions=True,stack_name=stack_name,
                                                                                          capabilities=[cloudformation.CloudFormationCapabilities.NAMED_IAM])
        pipeline = codepipeline.Pipeline(self,repo+'-pipeline',stages=[codepipeline.StageProps(actions=[github_source_action],stage_name='Source'),
                                                               codepipeline.StageProps(actions=[code_build_action],stage_name='Build'),
                                                               codepipeline.StageProps(actions=[update_stack_action],stage_name='Deploy')])
        return pipeline
```

The code build action requires a buildspec.yaml file to let it know what to do during the build.  Below I have pasted ours with some redactions denoted
with `<<` and `>>`.  Since the common repo is also kept private I had to modify the requirements.txt to get the repo using an https url.
```
version: '0.2'
env:
  secrets-manager:
    oauth_token: <<secret_name>>
phases:
  install:
    commands:
      - npm i -g aws-cdk
      - cdk --version
  build:
    commands:
      - . .env/bin/activate
      - sed -i s%git@github.com:<<function_org>>/<<function_repo>>%https://<<user>>:${oauth_token}@github.com/<<function_org>>>/<<function_repo>>%g requirements.txt
      - pip3 install -r requirements.txt
      - cdk synth
artifacts:
  files:
    - 'cdk.out/*'
  name: cfn_templates
  ```
