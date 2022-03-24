# Repo for CDKTF Sample Workouts
## Table of Contents
- [Introduction](#introduction)
- [What is CDKTF](#what-is-cdktf)
- [How CDKTF works](#how-cdktf-works)
- [Use cases for Choosing CDKTF](#use-cases-for-choosing-cdktf)
- [Steps to install CDK for Terraform](#steps-to-install-cdk-for-terraform)
  - [Step 1 Setting up Pre Requisites](#step-1-setting-up-pre-requisites)
      - [Install Terraform](#install-terraform)
      - [Install node package manager](#install-node-package-manager)
      - [Install pipenv](#install-pipenv)
      - [Configure AWS Cloud Credentials](#configure-aws-cloud-credentials)
  - [Step 2 Install CDKTF](#step-2-install-cdktf)
  - [Step 3 Create CDKTF project](#step-3-create-cdktf-project)
  - [Step 4 Defining AWS Infrastructure using Python](#step-4-defining-aws-infrastructure-using-python)
  - [Step 5 Spinning up Infra](#step-5-spinning-up-infra)
      - [pipenv install](#pipenv-install)
      - [cdktf get](#cdktf-get)
      - [cdktf synth](#cdktf-synth)
      - [cdktf deploy](#cdktf-deploy)
- [Cleanup of Demo](#cleanup-of-demo)
  - [cdktf destroy](#cdktf-destroy)
- [Components and Architecture](#components-and-architecture)
- [CDK for Terraform some key concepts](#cdk-for-terraform-some-key-concepts)
  - [Constructs](#constructs)
  - [Provider](#provider)
  - [Resources](#resources)
  - [Modules](#modules)
- [Conclusion](#conclusion)
- [Bibliography and Reference](#bibliography-and-reference)

## Introduction
Terraform is one of the most widely used tools for provisioning infrastructure as code. It is written using `Hashicorp Language` or `HCL` in short.
During the year 2021 Hashicorp introduced support for creating infrastructure using most widely used programming languages and named it as `Cloud Development Kit for Terraform (CDK for Terraform)` or `CDKTF` in short.

In this article we will see how to get started with creating infrastructure in CDKTF for AWS Cloud provider. I'll be using `Python` language to create infrastructure in AWS using CDKTF

Hashicorp introduced Terraform CDK, due to growing popularity for supporting Programming language based Infrastructure as code tools. Other tools are [AWS CDK](https://docs.aws.amazon.com/cdk/v2/guide/home.html) and [Pulumi](https://www.pulumi.com/docs/). 

## What is CDKTF
As per the [Terraform CDKTF documentation](https://www.terraform.io/cdktf)
> Cloud Development Kit for Terraform (CDKTF) allows you to use familiar programming languages to define and provision infrastructure. This gives you access to the entire Terraform ecosystem without learning HashiCorp Configuration Language (HCL) and lets you leverage the power of your existing toolchain for testing, dependency management, etc.

CDKTF currently support TypeScript, Python, Java, C#and Go (experimental) and CDK for Terraform are currently in beta(as on Mar-2022).

## How CDKTF works 
> CDK for Terraform leverages concepts and libraries from the AWS Cloud Development Kit to translate your code into infrastructure configuration files for Terraform.

> At a high level, you will:

> Create an Application: Use either a built-in or a custom template to scaffold a project in your chosen language.

> Define Infrastructure: Use your chosen language to define the infrastructure you want to provision on one or more providers. CDKTF automatically extracts the schema from Terraform providers and modules to generate the necessary classes for your application.

> Deploy: Use cdktf CLI commands to provision infrastructure with Terraform or synthesize your code into a JSON configuration file that others can use with Terraform directly.

## Use cases for Choosing CDKTF

CDKTF offers many benefits, but it is not the right choice for every project. Below are possible scenarios to consider using CDKTF:
>
- If you have a strong preference or need to use a procedural language to define infrastructure.
- If you need to create abstractions to help manage complexity. For example, you want to create constructs to model a reusable infrastructure pattern composed of multiple resources and convenience methods.
- You are comfortable doing your own troubleshooting and do not require commercial support.
- You can make this choice for each team and project because CDK for Terraform interoperates with existing Terraform providers and modules.

## Steps to install CDK for Terraform
- Now, let us get into some hands-on with CDKTF

### Step 1 Setting up Pre Requisites
- We need to install `Terraform` and `NodeJS` for CDKTF
- `Python PIP` for developing Python language based infrastructure definition using CDKTF
- Also we need to configure AWS credentials for setting up infra in AWS

### Install Terraform 
- Go to [Terraform Downloads](https://www.terraform.io/downloads.html) and install `Terraform v1.0+`

### Install node package manager
- Go to `nodesource` repo in GitHub and [follow the instructions](https://github.com/nodesource/distributions/blob/master/README.md) to install NodeJS

### Install pipenv
- Install `pipenv` Python package manager using the command

```
sudo apt install pipenv
```

### Configure AWS Cloud Credentials
- Terraform by default uses AWS credentials, which are available in either of `Environment Variables`, or `Shared Credentials` files
- Refer Terraform [AWS authentication docs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication-and-configuration) for setting up AWS auth for Terraform

### Step 2 Install CDKTF  
- Install CDK latest version using the command,

```
npm install --global cdktf-cli@latest
```

- Verify the installation, by running the command,

```
cdktf help
```

Now, we have everything required to proceed with creating an Infrastructure in AWS using Python

## Step 3 Create CDKTF project 
- New users can run the command `cdktf init` to create a cdktf project for specific programming language
- cdktf init command creates the required files needed to get started with developing infra using the programming language
- Below is the sample of `cdktf init` output, which shows the options of choosing project details and programming language
- I've created a sample directory called `aws-tier` for demo purpose

```
$ mkdir aws-tier
$ cd aws-tier

$ cdktf init
The 'npm view' command generated an error stream with content [npm notice 
npm notice New patch version of npm available! 8.5.2 -> 8.5.5
npm notice Changelog: <https://github.com/npm/cli/releases/tag/v8.5.5>
npm notice Run `npm install -g npm@8.5.5` to update!
npm notice]
Newer version of Terraform CDK is available [0.9.4] - Upgrade recommended
Welcome to CDK for Terraform!

By default, cdktf allows you to manage the state of your stacks using Terraform Cloud for free.
cdktf will request an API token for app.terraform.io using your browser.

If login is successful, cdktf will store the token in plain text in
the following file for use by subsequent Terraform commands:
    USER_HOME_DIR/.terraform.d/credentials.tfrc.json

Note: The local storage mode isn't recommended for storing the state of your stacks.

? Do you want to continue with Terraform Cloud remote state management? No
? What template do you want to use? python-pip

Initializing a project using the python-pip template.
? projectName: aws-tier
? projectDescription: A simple getting started project for cdktf.
Requirement already satisfied: cdktf~=0.8.3 in USER_HOME_DIR/.local/lib/python3.9/site-packages (from -r requirements.txt (line 1)) (0.8.3)
Requirement already satisfied: jsii<2.0.0,>=1.47.0 in USER_HOME_DIR/.local/lib/python3.9/site-packages (from cdktf~=0.8.3->-r requirements.txt (line 1)) (1.50.0)
Requirement already satisfied: publication>=0.0.3 in USER_HOME_DIR/.local/lib/python3.9/site-packages (from cdktf~=0.8.3->-r requirements.txt (line 1)) (0.0.3)
Requirement already satisfied: constructs<11.0.0,>=10.0.12 in USER_HOME_DIR/.local/lib/python3.9/site-packages (from cdktf~=0.8.3->-r requirements.txt (line 1)) (10.0.14)
Requirement already satisfied: python-dateutil in /usr/lib/python3/dist-packages (from jsii<2.0.0,>=1.47.0->cdktf~=0.8.3->-r requirements.txt (line 1)) (2.8.1)
Requirement already satisfied: attrs~=21.2 in USER_HOME_DIR/.local/lib/python3.9/site-packages (from jsii<2.0.0,>=1.47.0->cdktf~=0.8.3->-r requirements.txt (line 1)) (21.2.0)
Requirement already satisfied: typing-extensions<5.0,>=3.7 in USER_HOME_DIR/.local/lib/python3.9/site-packages (from jsii<2.0.0,>=1.47.0->cdktf~=0.8.3->-r requirements.txt (line 1)) (4.0.1)
Requirement already satisfied: cattrs<1.10,>=1.8 in USER_HOME_DIR/.local/lib/python3.9/site-packages (from jsii<2.0.0,>=1.47.0->cdktf~=0.8.3->-r requirements.txt (line 1)) (1.9.0)
========================================================================================================

  Your cdktf Python project is ready!

  cat help                Prints this message

  Compile:
    python3 ./main.py     Compile and run the python code.

  Synthesize:
    cdktf synth [stack]   Synthesize Terraform resources to cdktf.out/

  Diff:
    cdktf diff [stack]    Perform a diff (terraform plan) for the given stack

  Deploy:
    cdktf deploy [stack]  Deploy the given stack

  Destroy:
    cdktf destroy [stack] Destroy the given stack

  Learn more about using modules and providers https://cdk.tf/modules-and-providers

========================================================================================================

```
- Also one can run the below command to choose `python-pip` based template and `local` backend option
```
cdktf init --template="python-pip" --local
```

- We can see the below files has been created by `cdktf init` command

```
$ ls
cdktf.json  help  main.py  requirements.txt
```
## Step 4 Defining AWS Infrastructure using Python
- Clone the sample repo from GitHub
- We need can see multiple files, but there are two is our interest
  - File1: `main.py` - This is main file and it contains the infra definition in `python` programming language

```python
#!/usr/bin/env python
from constructs import Construct
from cdktf import App, TerraformStack, TerraformOutput
from imports.aws import AwsProvider, ec2


class CDKDemoStack(TerraformStack):
  def __init__(self, scope: Construct, ns: str):
    super().__init__(scope, ns)

    AwsProvider(self, "Aws", region="us-west-2")

    cdkdemoInstance = ec2.Instance(self, "cdkdemo",
      ami="ami-005e54dee72cc1d00",
      instance_type="t2.micro",
    )

    TerraformOutput(self, "cdkdemo_public_ip",
      value=cdkdemoInstance.public_ip
    )

app = App()
CDKDemoStack(app, "cdkdemo-terraform")

app.synth()
```

  - File2: `cdktf.json` - This files contains the Cloud provider version definition, also contains the reference to Terraform modules
- I've modified the `terraformProviders` and added `"hashicorp/aws@~> 3.67.0"`

```json
{
  "language": "python",
  "app": "pipenv run python main.py",
  "projectId": "project_id_generated_by_init",
  "terraformProviders": [
    "hashicorp/aws@~> 3.67.0"
  ],
  "terraformModules": [],
  "codeMakerOutput": "imports",
  "context": {
    "excludeStackIdFromLogicalIds": "true",
"allowSepCharsInLogicalIds": "true"
  }
}
```

- Copy the content of the above files into respective files in our demo project `aws-tier` sample

## Step 5 Spinning up Infra

### pipenv install
- Since we choose the `python.pip` template, we need to run the `pipenv install` command.
- It converts the `requirements.txt` file into `Pipfile`, and also creates `Pipfile.lock`
```
$ pipenv install
requirements.txt found, instead of Pipfile! Converting…
Warning: Your Pipfile now contains pinned versions, if your requirements.txt did. 
We recommend updating your Pipfile to specify the "*" version, instead.
Pipfile.lock not found, creating…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
Updated Pipfile.lock (d69941)!
Installing dependencies from Pipfile.lock (d69941)…
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 9/9 — 00:00:02
To activate this project's virtualenv, run the following:
 $ pipenv shell
```
### cdktf get
- This command generates CDK Constructs for Terraform providers and modules in the project
- Run the command `cdktf get` to fetch the required AWS resources and it is stored into `imports` directory 
- Please note, `import` directory is similar to `.terraform` temporary directory and to be ignored while pushing in the code to source control like git (check .gitignore file)
```
$ cdktf get
Generated python constructs in the output directory: imports
```

### cdktf synth
- Synthesizes Terraform code for the given app in a directory.
- This command creates `tfstate` file
```
$ cdktf synth
Generated Terraform code for the stacks: cdkdemo-terraform
```
- The above command will create a folder called `cdktf.out` that contains all Terraform JSON configuration that was generated.

### cdktf deploy
- Deploy and create infra in the specified provider
```
$ cdktf deploy
Deploying Stack: cdkdemo-terraform
Resources
 ✔ AWS_INSTANCE         cdkdemo             aws_instance.cdkdemo

Summary: 1 created, 0 updated, 0 destroyed.

Output: cdkdemo_public_ip = 34.209.44.175

```
- While running the above command, `tfstate.lock` file will be created in the current directory, because we chose to have a local backend while setting up the project.

## Cleanup of Demo
### cdktf destroy
- Destroy the created infra using this command
```
$ cdktf destroy
Destroying Stack: cdkdemo-terraform
Resources
 ✔ AWS_INSTANCE         cdkdemo             aws_instance.cdkdemo

Summary: 1 destroyed.

```
## Components and Architecture
As per the architecture explained in [documentation](https://www.terraform.io/cdktf/concepts/cdktf-architecture)

> CDKTF synthesizes infrastructure that you define in a supported programming language into JSON configuration files that Terraform can use to manage infrastructure.

> The diagram below shows how synthesizing a CDKTF application produces a series of artifacts in a designated output folder.
![CDKTF Execution Architecture](https://mktg-content-api.vercel.app/api/assets?product=terraform-cdk&version=main&asset=website/docs/cdktf/concepts/images/cdktf-terraform-workflow.png)

## CDK for Terraform some key concepts
- We need to note some key concepts in CDK for Terraform: 1. Constructs, 2. Provider, 3. Modules and 4. Resources

As per the CDKTF Documentation:

### Constructs
Construct is a general term that you can use to describe parts of your CDK for Terraform (CDKTF) project because every element in a CDKTF application is a descendent of the Construct base class. The entire application, each stack, and each resource are all constructs.

### Provider
A provider is a Terraform plugin that allows users to manage an external API. Provider plugins like the AWS provider or the cloud-init provider act as a translation layer that allows Terraform to communicate with many different cloud providers, databases, and services.

### Resources 
They are the most important element when defining infrastructure in CDKTF applications. Each resource describes one or more infrastructure objects, such as virtual networks, compute instances, or higher-level components such as DNS records.

### Modules
A Terraform module is a single directory that contains one or more configuration files.

Modules let you reuse configurations across projects and teams, saving time, enforcing consistency, and reducing errors. For example, you could create a module to describe the configuration for all of your organization's public website buckets. When you package and share this module, other users can incorporate it into their configurations. As requirements evolve, you can make changes to your module once, release a new version, and apply those changes everywhere that module is used.

## Conclusion
In this article, I've covered how to get started with CDKTF, Concepts of cdktf, Installation and Project setup.

Also I've explained about some important commands to setup and spin up the infrastructure in AWS.

Hope this is helpful.

## Bibliography and Reference
- [AWS Infra Creation using Python-pip](https://github.com/hashicorp/terraform-cdk/tree/main/examples/python/aws)

- [CDKTF Examples](https://www.terraform.io/cdktf/examples)

- [CDKTF](https://www.terraform.io/cdktf)

- [Hashicorp Learn](https://learn.hashicorp.com/tutorials/terraform/cdktf-build?in=terraform/cdktf)

## Follow me and share your thoughts,
- [LinkedIn](https://www.linkedin.com/in/saravanan-gnanaguru-1941a919/)
- [Twitter](https://twitter.com/saransid)
- [GitHub](https://github.com/chefgs)
- [gsaravanan.dev](https://gsaravanan.dev)
