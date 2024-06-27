# Scripting in .gitlab-ci.yml

## Motivation

Although I think that scripts used in pipelines should be written in separate files, sometimes you might want some shell commands that depend on the repository provider outside of your scripts, in the pipeline file itself.

While it is easy to add a single command like this:
```sh
  script:
    - echo "Hello World!"
```
, how do you add something more complex like:
```sh
      if [ "$CI_COMMIT_REF_NAME" = "master" ]; then
        export DEPLOYMENT_TARGET="production"
      else
        export DEPLOYMENT_TARGET="development"
      fi
```
?

Obviously:
```sh
  script:
    - if [ "$CI_COMMIT_REF_NAME" = "master" ]; then
    - export DEPLOYMENT_TARGET="production"
    - else
    - export DEPLOYMENT_TARGET="development"
    - fi
```
will not work because each `-` will expect a separate command.

## Prerequisites

A `GitLab` repository where you can run your pipeline.

## Implementation

The correct way to add the code to the `script` section of the pipeline is:
```sh
  script:
    - >
      if [ "$CI_COMMIT_REF_NAME" = "master" ]; then
        export DEPLOYMENT_TARGET="production"
      else
        export DEPLOYMENT_TARGET="development"
      fi
```
Note: if your code would produce some new variable, it is a good idea to use an environment variable, since it does not have to be passed on from script to script. This can make life much easier when running your main scripts from docker containers.
