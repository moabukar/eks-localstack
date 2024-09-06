# EKS on localstack

- Terraform + AWS / LocalStack

## Prerequisites

* Docker
* [Helm](https://helm.sh/docs/intro/install/)
* [Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
* [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
* [`awscli-local`](https://github.com/localstack/awscli-local)
* [`terraform-local`](https://github.com/localstack/terraform-local)
* [pre-commit](https://github.com/pre-commit/pre-commit-hooks) - required only for staging changes to the repository

For :apple: macOS users, you can use the [`Brewfile`](Brewfile) script to simplify the installation of all the
necessary dependencies.

If you have Homebrew installed, follow these steps:

```bash
brew bundle
```

## :rocket: Getting Started

### :house: LocalStack

* Install LocalStack AWS CLI (`awslocal`) and `tflocal`

* Run LocalStack:

    * :free: Community:

      ```bash
      docker-compose -f localstack-compose.yml up
      ```

    * :money_with_wings: Pro:

      required, for example, for the EKS component. more
      info: [LocalStack Coverage](https://docs.localstack.cloud/references/coverage/)

      :warning: You need to provide `LOCALSTACK_API_KEY` value.

      ```bash
      # Set up the API Key
      export LOCALSTACK_API_KEY=<your-api-key>

      # Run LocalStack Pro
      docker-compose -f localstack-pro-compose.yml up
      ```
```bash
##or you can just do 

you need localstack pro (you can get free trial)

export LOCALSTACK_AUTH_TOKEN=<>
localstack start # in another terminal
```

* Initialize Terraform:

```bash
tflocal init
```

* Generate Terraform plan:

```bash
tflocal plan
```

* Apply Terraform configuration:

```bash
tflocal apply -auto-approve
```

### :cloud: AWS

* Create S3 state bucket

```bash
aws s3api create-bucket --bucket tf-state
```

* Initialize Terraform:

```bash
terraform init
```

* Generate Terraform plan:

```bash
terraform plan
```

* Apply Terraform configuration:

```bash
terraform apply -auto-approve
```

## Connecting to EKS on LocalStack using `kubectl`

**1. Update kubeconfig for EKS Cluster**

First, you need to add the `my-eks` cluster information to your `~/.kube/config` to enable `kubectl` to know where your
cluster is and how to access it.

```bash
awslocal eks update-kubeconfig --name my-eks
```

**2. Configure AWS CLI for LocalStack**

Before interacting with LocalStack, configure the AWS CLI to use dummy credentials.
Though LocalStack doesn't validate these credentials, it expects them to be set.

Run the configuration command:

```bash
aws configure
```

When the AWS CLI prompts you for the credentials, use dummy credentials:

```bash
AWS Access Key ID [None]: test
AWS Secret Access Key [None]: test
```

**3. Set current k8s context**

```

brew install kubectx

kubectx arn:aws:eks:us-east-1:000000000000:cluster/my-eks
```

**4. Validate kubectl Configuration**

List resources across all namespaces to validate the connection:

```bash
kubectl get all -A
```
