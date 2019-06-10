Discovered this cool tool that generates documentation for terraform modules:
https://github.com/segmentio/terraform-docs and you can copy the output into
your README.  Or go a step further and have it auto populated into your README
with this tool: https://github.com/cytopia/docker-terraform-docs.  He even
includes steps for how to set it up to fail on pull requests.  I did this but
went a step further and made another job to run `terraform fmt` and then do
the git diff to see if there are differences.
