# Availables Image Groups

- [abdennour/ansible](https://hub.docker.com/r/abdennour/ansible)

    |_ **abdennour/ansible:x.y.z** (Full for DEV and CI)
    |_ **abdennour/ansible:x.y.z-slim** (Light)

- [abdennour/aws](https://hub.docker.com/r/abdennour/aws)

    |_ **abdennour/aws:x.y.z**

- [abdennour/docker](https://hub.docker.com/r/abdennour/docker)

    |_ **abdennour/docker:x.y-dind**
    |_ **abdennour/docker:x.y-dind-awsx.y.z**

- [abdennour/eksctl](https://hub.docker.com/r/abdennour/eksctl)

    |_ **abdennour/eksctl:x.y.z-aws-x.y.z**

- [abdennour/envsubst](https://hub.docker.com/r/abdennour/envsubst)

- [abdennour/kubectl](https://hub.docker.com/r/abdennour/kubectl)

    |_ **abdennour/kubectl:vx.y.z**
    |_ **abdennour/kubectl:vx.y.z-awsx.y.z**

- [abdennour/nexus3-cli](https://hub.docker.com/r/abdennour/nexus3-cli)

    |_ **abdennour/nexus3-cli:vx.y.z**

- [abdennour/rhel](https://hub.docker.com/r/abdennour/rhel)

    |_ **abdennour/rhel:x**


# Examples

## [abdennour/ansible](https://hub.docker.com/r/abdennour/ansible)

**abdennour/ansible:x.y.z** (Full for DEV and CI)

```sh
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);

# basic example
docker run -it --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$(pwd):/playbook" \
  -w /playbook 
  -e DOCKER_GID_ON_HOST=$(cat /etc/group | grep docker: | cut -d: -f3) \
  -e SSH_PRIVATE_KEY_B64=$(cat ~/.ssh/id_rsa | base64) \

  abdennour/ansible:${ANSIBLE_VERSION} bash;
### Then interactively
ansible --version
## SSH client is available
cat ~/.ssh/id_rsa.pub # autogenerated if you don't pass the ENV VAR SSH_PRIVATE_KEY_B64
ssh x.y.z.y;

## Docker in Docker is available also
ansible mycontainerIdOrName -m ping -e "ansible_connection=docker"
## Molecule is also available
molecule test
## Ansible Lint is also available
ansible-lint *.yml

```

**abdennour/ansible:x.y.z-slim** (Light)

```sh
## A light ansible container
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);

docker run -it --rm \
  abdennour/ansible:${ANSIBLE_VERSION}-slim \
  ansible --version

```


## [abdennour/aws](https://hub.docker.com/r/abdennour/aws)

**abdennour/aws:x.y.z**

```sh
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);

# basic example
docker run --rm \
  -v "${HOME}/.aws:/root/.aws" \
  abdennour/aws:${AWS_CLI_VERSION} s3 ls

```

## [abdennour/docker](https://hub.docker.com/r/abdennour/docker)

**abdennour/docker:x.y-dind**

```sh
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);

# basic example
docker run --rm \
  -v /var/run/docker.sock:/var/run/docker.sock \
  abdennour/docker:${DOCKER_VERSION} docker images

```

**abdennour/docker:x.y-dind-awsx.y.z**

```sh
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);

# docker + aws
docker run --name docker-aws --rm -d \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "${HOME}/.aws:/root/.aws" \
  abdennour/docker:${DOCKER_VERSION}-aws${AWS_CLI_VERSION} tail -f /dev/null
## then interactively
docker exec docker-aws sh -c '$(aws ecr get-login --no-include-email --region us-west-1)';
docker exec docker-aws docker pull xxxxxxxx.dkr.ecr.us-west-1.amazonaws.com

```

## [abdennour/eksctl](https://hub.docker.com/r/abdennour/eksctl)

**abdennour/eksctl:x.y.z-aws-x.y.z**

```sh
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);

# basic example
docker run --rm \
  -v "${HOME}/.aws:/root/.aws" \
  -e AWS_PROFILE=my-aws-profile \  
  abdennour/eksctl:${EKSCTL_VERSION}-aws-${AWS_CLI_VERSION} create cluster ...
```

## [abdennour/envsubst](https://hub.docker.com/r/abdennour/envsubst)

```sh
# basic example
cat file_includes_env_vars.txt | docker run -i --rm abdennour/envsubst
# or 
docker run -i --rm abdennour/envsubst < file_includes_env_vars.txt

# assume "Hello $NAME. My home is ${HOME}" is the content of "file.txt"
docker run -i --rm -e NAME=Abdou abdennour/envsubst < file.txt
# the above command outputs :
# "Hello Abdou. My home is /root"
```

## [abdennour/jenkins](https://hub.docker.com/r/abdennour/jenkins)
TODO


## [abdennour/kubectl](https://hub.docker.com/r/abdennour/kubectl)

**abdennour/kubectl:vx.y.z**

```sh
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);

# basic example
docker run --rm \
  -v "${HOME}/.kube:/kube" \
  -e KUBECONFIG=/kube/config \
abdennour/kubectl:${KUBECTL_VERSION} get pods

```

**abdennour/kubectl:vx.y.z-awsx.y.z**

```sh
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);

# kubectl + aws-iam-authenticator for EKS
docker run --rm \
  -v "${HOME}/.aws:/root/.aws" \
  -e AWS_PROFILE=my-aws-profile \
  -e AWS_DEFAULT_REGION=md-west-1 \
  -e CLUSTER_NAME=mycluster \
abdennour/kubectl:${KUBECTL_VERSION}-aws${AWS_CLI_VERSION} get pods
```

## [abdennour/nexus3-cli](https://hub.docker.com/r/abdennour/nexus3-cli)

This is an image for https://pypi.org/project/nexus3-cli/

**abdennour/nexus3-cli:vx.y.z**

```sh
export $(curl -SsL https://raw.githubusercontent.com/abdennour/dockerfiles/master/.env | xargs);
alias nexus3=' docker run --rm -t -v nexus3-cli:/root  abdennour/nexus3-cli:v${NEXUS3CLI_VERSION}'
# login
 nexus3  login
# Nexus OSS URL (http://localhost:8081):
# Nexus admin username (admin):
# Nexus admin password (admin123):
# Verify server certificate (True):

# Configuration saved to /root/.nexus-cli

#----
```

More about available commands: https://pypi.org/project/nexus3-cli/


## [abdennour/node](https://hub.docker.com/r/abdennour/node)
TODO

## [abdennour/remark](https://hub.docker.com/r/abdennour/remark)
TODO

## [abdennour/rhel](https://hub.docker.com/r/abdennour/rhel)

Ready to be used as Ansible Managed Host.

**abdennour/rhel:x**
```sh
docker run -it --rm \
  --name rhel8 \
  -v /sys/fs/cgroup:/sys/fs/cgroup:ro \
  -p 6666:80
  --privileged \
  abdennour/rhel:8

# then
docker exec -it rhel8 yum install nginx -y
docker exec -it rhel8 systemctl start nginx

# In the host navigate to : http://localhost:6666
```


# CI/CD 

Managed By [Docker hub automated builds](https://docs.docker.com/docker-hub/builds/)

#  Tests

**Linting** : in `docker-images/<IMAGE>/hooks/pre_build`
**Contatainer Structure Tests** in `docker-images/<IMAGE>/hooks/build`
