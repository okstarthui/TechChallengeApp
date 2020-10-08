# Deployment via Terraform
## Gettings started:

- Install Docker https://runnable.com/docker/getting-started/
- Install AWS CLI https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
- Configure AWS profile: https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html


## make sure you have install kubectl

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.13.10/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

## make sure you have install aws-iam-authenticator

```
curl -LO https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.4.0/aws-iam-authenticator_0.4.0_linux_amd64
chmod +x ./aws-iam-authenticator_0.4.0_linux_amd64
sudo mv aws-iam-authenticator_0.4.0_linux_amd64 /usr/local/bin/aws-iam-authenticator
```

## Run Command:
```
docker run -ti --rm -v ${HOME}/.aws:/root/.aws -v ${HOME}/.ssh:/root/.ssh -e AWS_SDK_LOAD_CONFIG=1 -v ${pwd}:/apps -w /apps hashicorp/terraform:light init

docker run -ti --rm -v ${HOME}/.aws:/root/.aws -v ${HOME}/.ssh:/root/.ssh -e AWS_SDK_LOAD_CONFIG=1 -v ${pwd}:/apps -w /apps hashicorp/terraform:light plan

docker run -ti --rm -v ${HOME}/.aws:/root/.aws -v ${HOME}/.ssh:/root/.ssh -e AWS_SDK_LOAD_CONFIG=1 -v ${pwd}:/apps -w /apps hashicorp/terraform:light apply
```

## Design

Terraform will create the following:

- VPC for the application, subnets, routing tables, etc.
- Postgres Aurora RDS
- Load balancer (listenting port 80, open to the world)
- Create a ECS cluster
- Create a ECS service
- Create a ECS tasks passing in database creditials

The application will be deployed into AWS Fargate to run the container. The containers will have access to the Aurora DB. The load balancer will provide access to the container ports 3000 via port 80 publicly.

## Database

The database will need to be initally created by running a task with the `updatedb` command.

## Accessing the app

The application should be accessable via the Load balancer DNS.