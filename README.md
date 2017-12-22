# aws-ecs-deploy

Deploys an ecs service by creating a new task definition based on the latest task definition for that service, then updating the service with that new task defintion

Single account:

    docker run -e AWS_ACCESS_KEY_ID=xxx -e AWS_SECRET_ACCESS_KEY=yyy -e AWS_REGION=us-east-1 simplygenius/aws-ecs-deploy ecs-deploy cluster_name service_name image

Assuming role across multiple accounts:

    docker run -e AWS_ACCESS_KEY_ID=xxx -e AWS_SECRET_ACCESS_KEY=yyy -e AWS_REGION=us-east-1 simplygenius/aws-ecs-deploy aws-assume-role account_id role_name ecs-deploy cluster_name service_name image


# Usage example with travis

```
language: generic
sudo: false

services:
  - docker

env:
  global:
    # secure contains the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY for use in ecs deploy:
    # travis encrypt AWS_ACCESS_KEY_ID="key" AWS_SECRET_ACCESS_KEY="secret"
    #
    - secure: "xxx"

    - SERVICE_NAME=<your ecs app name>
    - ENV=dev
    - AWS_ACCOUNT=1234567890
    - AWS_REGION=us-east-1
    - ECS_CLUSTER=main
    - NAME_PREFIX=
    
    - MOUNTS="-v /var/run/docker.sock:/var/run/docker.sock -v $PWD:/app"
    - DOCKER_OPTS="-e AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY -e AWS_DEFAULT_REGION $MOUNTS"

install: true

after_success:
bash -x /usr/local/bin/deploy -a 500651484941 -c dev-botchain-ecs-main -d dev-deployer dev-botchain-botchain-api
  - docker run $DOCKER_OPTS simplygenius/aws-ecs-deploy deploy \
    -a $AWS_ACCOUNT \
    -c $NAME_PREFIX$ECS_CLUSTER \
    -d $ENV-deployer \
    -v ${TRAVIS_COMMIT::7} \
    $NAME_PREFIX$SERVICE_NAME 
```
