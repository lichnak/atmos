# atmos [![Latest Release](https://img.shields.io/github/release/cloudposse/atmos.svg)](https://github.com/cloudposse/atmos/releases/latest) [![Slack Community](https://slack.cloudposse.com/badge.svg)](https://slack.cloudposse.com)

[![README Header][readme_header_img]][readme_header_link]

[![Cloud Posse][logo]](https://cpco.io/homepage)

<!--




  ** DO NOT EDIT THIS FILE
  **
  ** This file was automatically generated by the `build-harness`.
  ** 1) Make all changes to `README.yaml`
  ** 2) Run `make init` (you only need to do this once)
  ** 3) Run`make readme` to rebuild this file.
  **
  ** (We maintain HUNDREDS of open source projects. This is how we maintain our sanity.)
  **





-->

Universal Tool for DevOps and Cloud Automation.


---

This project is part of our comprehensive ["SweetOps"](https://cpco.io/sweetops) approach towards DevOps.
[<img align="right" title="Share via Email" src="https://docs.cloudposse.com/images/ionicons/ios-email-outline-2.0.1-16x16-999999.svg"/>][share_email]
[<img align="right" title="Share on Google+" src="https://docs.cloudposse.com/images/ionicons/social-googleplus-outline-2.0.1-16x16-999999.svg" />][share_googleplus]
[<img align="right" title="Share on Facebook" src="https://docs.cloudposse.com/images/ionicons/social-facebook-outline-2.0.1-16x16-999999.svg" />][share_facebook]
[<img align="right" title="Share on Reddit" src="https://docs.cloudposse.com/images/ionicons/social-reddit-outline-2.0.1-16x16-999999.svg" />][share_reddit]
[<img align="right" title="Share on LinkedIn" src="https://docs.cloudposse.com/images/ionicons/social-linkedin-outline-2.0.1-16x16-999999.svg" />][share_linkedin]
[<img align="right" title="Share on Twitter" src="https://docs.cloudposse.com/images/ionicons/social-twitter-outline-2.0.1-16x16-999999.svg" />][share_twitter]




It's 100% Open Source and licensed under the [APACHE2](LICENSE).












## Introduction


`atmos` is both a library and a command-line tool for provisioning, managing and orchestrating workflows across various toolchains.
We use it extensively for automating cloud infrastructure and [Kubernetes](https://kubernetes.io/) clusters.

`atmos` includes workflows for dealing with:

   - Provisioning [Terraform](https://www.terraform.io/) projects
   - Deploying [helm](https://helm.sh/) [charts](https://helm.sh/docs/topics/charts/) to Kubernetes clusters using [helmfiles](https://github.com/roboll/helmfile)
   - Executing [helm](https://helm.sh/) commands on Kubernetes clusters
   - Provisioning [istio](https://istio.io/) on Kubernetes clusters using [istio operator](https://istio.io/latest/blog/2019/introducing-istio-operator/) and helmfile
   - Executing [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) commands on Kubernetes clusters
   - Executing [AWS SDK](https://aws.amazon.com/tools/) commands to orchestrate cloud infrastructure
   - Running [AWS CDK](https://aws.amazon.com/cdk/) constructs to define cloud resources
   - Executing commands for the [serverless](https://www.serverless.com/) framework
   - Executing shell commands
   - Combining various commands into workflows to execute many commands sequentially in just one step
   - ... and many more

In essence, it's a tool that orchestrates the other CLI tools in a consistent and self-explaining manner.
It's a superset of all other tools and task runners (e.g. `make`, `terragrunt`, `terraform`, `aws` cli, `gcloud`, etc)
and intended to be used to tie everything together, so you can provide a simple CLI interface for your organization.

Moreover, `atmos` is not only a command-line interface for managing clouds and clusters. It provides many useful patterns and best practices, such as:

- Enforces Terraform and helmfile projects' structure (so everybody knows where things are)
- Provides separation of configuration and code (so the same code could be easily deployed to different regions, environments and stages)
- It can be extended to include new features, commands, and workflows
- The commands have a clean, consistent and easy to understand syntax
- The CLI can be compiled into a binary and included in other tools and containers for DevOps, cloud automation and CI/CD
- The CLI code is modular and self-documenting


## CLI Structure

The CLI is built with [variant2](https://github.com/mumoshu/variant2) using [HCL syntax](https://www.terraform.io/docs/configuration/index.html).

`*.variant` files are combined like Terraform files.

See `variant` docs for more information on [writing commands](https://github.com/mumoshu/variant2#writing-commands).

The CLI code consists of self-documenting [modules](modules) (separating the files into modules is done for cleanliness):

  - shell - `shell` commands and helpers for the other modules
  - terraform - `terraform` commands (`plan`, `apply`, `deploy`, `destroy`, `import`, etc.)
  - helm - `helm` commands (e.g. `list`)
  - helmfile - `helmfile` commands (`diff`, `apply`, `deploy`, `destroy`, `sync`, `lint`, etc.)
  - kubeconfig - commands to download and manage `kubeconfig` from EKS clusters
  - istio - commands to manage `istio` using `istio-operator` and `helmfile`
  - workflow - commands to construct and execute cloud automation workflows


## Developing Your Own CLI

One way to use this project is by writing your own custom CLI that leverages our atmos.

This is ideal when you have your own workflows that you want to develop in addition to using the ones we've developed for you.

For example, maybe you have your own existing CLI tools (e.g. using `terragrunt`). In this case, you may want to start by developing your own CLI.


## Usage

There are a number of ways you can leverage this project:

  1. As a **standalone CLI** - you can use our CLI without any modification and get immediate gratification
  2. As a **library** - you can import our `variant` modules into your own CLI and expand the workflows for your needs
  3. As a **Docker image** - you can use our [Docker image](example/Dockerfile) the way you would the `cli` and run the workflows


## Recommended Layout

Our recommended filesystem layout looks like this:

  ~~~
     │   # CLI configuration
     └── cli/
     │  
     │   # Centralized project configuration
     ├── config/
     │   │
     │   └── $environment-$stage.yaml
     │  
     │   # Projects are broken down by tool
     ├── projects/
     │   │
     │   ├── terraform/   # root modules in here
     │   │   ├── vpc/
     │   │   ├── eks/
     │   │   ├── rds/
     │   │   ├── iam/
     │   │   ├── dns/
     │   │   └── sso/
     │   │
     │   └── helmfiles/  # helmfiles are organized by chart
     │       ├── cert-manager/helmfile.yaml
     │       └── external-dns/helmfile.yaml
     │  
     │   # Makefile for building the CLI
     ├── Makefile
     │  
     │   # Docker image for shipping the CLI and all dependencies
     └── Dockerfile (optional)

  ~~~


## Example

The [example](example) folder contains a complete solution that shows how to:

  - Structure the terraform and helmfile projects
  - Configure the CLI top-level module [main.variant](example/cli/main.variant)
  - Add [configurations](example/config) (variables) for the terraform and helmfile projects (to provision them to different environments and stages)
  - Create a [Dockerfile](example/Dockerfile) with commands to build and include the CLI into the container
  - Combine many CLI commands into workflows and run the workflows to provision resources

In the example, we show how to create and provision (using the CLI) the following resources for three different environments/stages:

  - VPCs for `dev`, `staging` and `prod` stages in the `us-east-2` region (which we refer to as `ue2` environment)
  - EKS clusters in the `ue2` environment for `dev`, `staging` and `prod`
  - `ingress-nginx` helmfile to be deployed on all the EKS clusters
  - `istio` helmfile and workflow to deploy `istio` on the EKS clusters using `istio-operator`


## CLI Configuration

  The CLI top-level module [main.variant](example/cli/main.variant) contains the global settings (options) for the CLI, including the location of the terraform projects,
  helmfiles, and configurations.

  It's configured for that particular example project, but can be changed to reflect the desired project structure.

In the example we have the following:

  - The terraform projects are in the [projects](example/projects) folder - we set that global option in [main.variant](example/cli/main.variant)
  ```hcl
    option "project-dir" {
      default     = "./"
      description = "Terraform projects directory"
      type        = string
    }
  ```

  - The helmfiles are in the [projects/helmfiles](example/projects/helmfiles) folder - we set that global option in [main.variant](example/cli/main.variant)
  ```hcl
    option "helmfile-dir" {
      default     = "./helmfiles"
      description = "Helmfile projects directory"
      type        = string
    }
  ```

  - The configurations (Terraform and helmfile variables) are in the [config](example/config) folder - we set that global option in [main.variant](example/cli/main.variant)
  ```hcl
    option "config-dir" {
      default     = "../config"
      description = "Config directory"
      type        = string
    }
  ```

__NOTE:__ The container starts in the [projects](example/projects) directory (see [Dockerfile](example/Dockerfile)).
All paths are relative to the [projects](example/projects) directory, but can be easily changed (in the Dockerfile and in the CLI options) as needed.

[main.variant](example/cli/main.variant) also includes the `imports` statement that imports all the required modules from the `atmos` repo.

__NOTE:__ For the example, we import all the CLI modules, but they could be included selectively depending on a particular usage.

  ```hcl
      imports = [
      "git::https://git@github.com/cloudposse/atmos@modules/shell?ref=master",
      "git::https://git@github.com/cloudposse/atmos@modules/kubeconfig?ref=master",
      "git::https://git@github.com/cloudposse/atmos@modules/terraform?ref=master",
      "git::https://git@github.com/cloudposse/atmos@modules/helmfile?ref=master",
      "git::https://git@github.com/cloudposse/atmos@modules/helm?ref=master",
      "git::https://git@github.com/cloudposse/atmos@modules/workflow?ref=master",
      "git::https://git@github.com/cloudposse/atmos@modules/istio?ref=master"
    ]
  ```

__NOTE:__ `imports` statement supports `https`, `http`, and `ssh` protocols.

__NOTE:__ The global options from [main.variant](example/cli/main.variant) are propagated to all the downloaded modules,
so they need to be specified only in one place - in the top-level module.

When we build the Docker image, all the modules from the `imports` statement are downloaded, combined with the top-level module [main.variant](example/cli/main.variant),
and compiled into a binary, which then included in the container.


## Centralized Project Configuration

`atmos` provides separation of configuration and code, allowing you to provision the same code into different regions, environments and stages.

In our example, all the code (Terraform and helmfiles) is in the [projects](example/projects) folder.

The centralized configuration (variables for the Terraform and helmfile projects) is in the [config](example/config) folder.

All configuration files are broken down by environments and stages and use the predefined format `$environment-$stage.yaml`.

Environments are abbreviations of AWS regions, e.g. `ue2` stands for `us-east-2`, whereas `uw2` would stand for `us-west-2`.

`$environment-globals.yaml` is where you define the global values for all stages for a particular environment.
The global values get merged with the `$environment-$stage.yaml` configuration for a specific environment/stage by the CLI.

In the example, we defined a few config files:

  - [ue2-dev.yaml](example/config/ue2-dev.yaml) - configuration (Terraform and helmfile variables) for the environment `ue2` and stage `dev`
  - [ue2-staging.yaml](example/config/ue2-staging.yaml) - configurations (Terraform and helmfile variables) for the environment `ue2` and stage `staging`
  - [ue2-prod.yaml](example/config/ue2-prod.yaml) - configurations (Terraform and helmfile variables) for the environment `ue2` and stage `prod`
  - [ue2-globals.yaml](example/config/ue2-globals.yaml) - global settings for the environment `ue2` (e.g. `namespace`, `region`, `environment`)

Each configuration file for an environment/stage has a predefined format:

  ```yaml
    projects:
      globals:
        stage: dev

      terraform:
        vpc:
          vars:

        eks:
          vars:

      helmfile:
        ingress-nginx:
          vars:

    workflows:
      deploy-all:

      istio-init:

      istio-destroy:
  ```

It has the following main sections:

  - `projects` - (required) configuration for the Terraform and helmfile projects for the environment/stage
  - `workflows` - (optional) workflow definitions for the environment/stage (see [Workflows](#Workflows) section below for the more detailed description of workflows)

The `projects` section consists of the following:

   - `globals` - global variables for the stage. Note that globals for the environment (in `$environment-globals.yaml` file) are merged with the globals for the stage
  and finally with the Terraform and helmfile variables for the environment/stage. For example, in [ue2-globals.yaml](example/config/ue2-globals.yaml) we defined
  `environment: ue2`, whereas in [ue2-dev.yaml](example/config/ue2-dev.yaml) we defined `stage: dev`. These values will be available as variables in the Terraform
  and helmfile projects

   - `terraform` - defines variables for each Terraform project. Terraform project names correspond to the Terraform projects in the [projects](example/projects) folder

   - `helmfile` - defines variables for each helmfile project. Helmfile project names correspond to the helmfile projects in the [helmfiles](example/projects/helmfiles) folder


## Run the Example

To run the example, execute the following commands in a terminal:

  - `cd example`
  - `make all` - it will build the Docker image, build the `atmos` CLI tool inside the image, and then start the container

Note that the name of the CLI executable is configurable.

In the [Dockerfile](example/Dockerfile) for the example, we've chosen the name `opsctl`, but it could be any name you want, for example
`ops`, `cli`, `ops-exe`, etc. The name of the CLI executable is configured using `ARG CLI_NAME=opsctl` in the Dockerfile.

After the container starts, run `opsctl help` to see the available commands and available flags.

__NOTE:__ We use the Cloud Posse [geodesic](https://github.com/cloudposse/geodesic) image as the base image for the container.
This is not strictly a requirement, but our base image ships with all the standard tools for cloud automation that we depend on (e.g. `terraform`, `helm`, `helmfile`, etc).


## Provision Terraform Project

To provision a Terraform project using the `opsctl` CLI, run the following commands in the container shell:

  ```bash
    opsctl terraform plan eks --environment=ue2 --stage=dev
    opsctl terraform apply eks --environment=ue2 --stage=dev
  ```

where:

  - `efs` is the Terraform project to provision (from the `projects` folder)
  - `--environment=ue2` is the environment to provision the project into (e.g. `ue2`, `uw2`). Note: the environments we are using here are abbreviations of AWS regions
  - `--stage=dev` is the stage/account (`prod`, `staging`, `dev`)

Short versions of the command-line arguments can be used:

  ```bash
    opsctl terraform plan eks -e ue2 -s dev
    opsctl terraform apply eks -e ue2 -s dev
  ```

To execute `plan` and `apply` in one step, use `terrafrom deploy` command:

  ```bash
    opsctl terraform deploy eks -e ue2 -s dev
  ```

## Provision Helmfile Project

To provision a helmfile project using the `opsctl` CLI, run the following commands in the container shell:

  ```bash
    opsctl helmfile diff ingress-nginx --environment=ue2 --stage=dev
    opsctl helmfile apply ingress-nginx --environment=ue2 --stage=dev
  ```

where:

  - `ingress-nginx` is the helmfile project to provision (from the `projects/helmfiles` folder)
  - `--environment=ue2` is the environment to provision the project into (e.g. `ue2`, `uw2`). Note: the environments we are using here are abbreviations of AWS regions
  - `--stage=dev` is the stage/account (`prod`, `staging`, `dev`)

Short versions of the command-line arguments can be used:

  ```bash
    opsctl helmfile diff ingress-nginx -e ue2 -s dev
    opsctl helmfile apply ingress-nginx -e ue2 -s dev
  ```

To execute `diff` and `apply` in one step, use `helmfile deploy` command:

  ```bash
    opsctl helmfile deploy ingress-nginx -e ue2 -s dev
  ```

## Deploy istio

To deploy `istio` into a Kubernetes cluster, run the following commands:

  ```bash
    opsctl istioctl operator-init -e ue2 -s dev
    opsctl helmfile deploy istio -e ue2 -s dev
  ```

This will install the `istio` operator first, then provision `istio` using the helmfile.


## Workflows

Workflows are a way of combining multiple commands into one executable unit of work.

In the CLI, workflows can be defined using two different methods:

  - In the configuration file for an environment/stage (see [workflows in ue2-dev.yaml](example/config/ue2-dev.yaml) for an example)
  - In a separate file (see [workflows-all.yaml](example/config/workflows-all.yaml) and [workflows-istio.yaml](example/config/workflows-istio.yaml))

In the first case, we define workflows in the configuration file for the environment and stage (which we specify on the command line).
To execute the workflows from [workflows in ue2-dev.yaml](example/config/ue2-dev.yaml), run the following commands:

  ```bash
    opsctl workflow deploy-all -e ue2 -s dev
    opsctl workflow istio-init -e ue2 -s dev
  ```

Note that workflows defined in the environment/stage config files can be executed only for the particular environment and stage.
It's not possible to provision resources for multiple environments and stages this way.

In the second case (defining workflows in a separate file), a single workflow can be created to provision resources into different environments/stages.
The environments/stages for the workflow steps can be specified in the workflow config.

For example, to run `terraform plan` and `helmfile diff` on all terraform and helmfile projects in the example, execute the following command:

  ```bash
    opsctl workflow plan-all -f workflows-all
  ```

where the command-line option `-f` (`--file` for long version) instructs the `opsctl` CLI to look for the `plan-all` workflow in the file [workflows-all](example/config/workflows-all.yaml).

As we can see, in multi-environment workflows, each workflow job specifies the environment and stage it's operating on:

  ```yaml
  workflows:
    plan-all:
      description: Run 'terraform plan' and 'helmfile diff' on all projects for all environments/stages
      steps:
        - job: terraform plan vpc
          environment: ue2
          stage: dev
        - job: terraform plan eks
          environment: ue2
          stage: dev
        - job: helmfile diff ingress-nginx
          environment: ue2
          stage: dev
        - job: terraform plan vpc
          environment: ue2
          stage: staging
        - job: terraform plan eks
          environment: ue2
          stage: staging
  ```

You can also define a workflow in a separate file without specifying the environment and stage in the workflow's job config.
In this case, the environment and stage need to be provided on the command line.

For example, to run the `deploy-all` workflow from the [workflows-all](example/config/workflows-all.yaml) file for the environment `ue2` and stage`dev`,
execute the following command:

  ```bash
    opsctl workflow deploy-all -f workflows-all -e ue2 -s dev
  ```









## Share the Love

Like this project? Please give it a ★ on [our GitHub](https://github.com/cloudposse/atmos)! (it helps us **a lot**)

Are you using this project or any of our other projects? Consider [leaving a testimonial][testimonial]. =)


## Related Projects

Check out these related projects.

- [variant2](https://github.com/mumoshu/variant2) - Turn your bash scripts into a modern, single-executable CLI app today



## Help

**Got a question?** We got answers.

File a GitHub [issue](https://github.com/cloudposse/atmos/issues), send us an [email][email] or join our [Slack Community][slack].

[![README Commercial Support][readme_commercial_support_img]][readme_commercial_support_link]

## DevOps Accelerator for Startups


We are a [**DevOps Accelerator**][commercial_support]. We'll help you build your cloud infrastructure from the ground up so you can own it. Then we'll show you how to operate it and stick around for as long as you need us.

[![Learn More](https://img.shields.io/badge/learn%20more-success.svg?style=for-the-badge)][commercial_support]

Work directly with our team of DevOps experts via email, slack, and video conferencing.

We deliver 10x the value for a fraction of the cost of a full-time engineer. Our track record is not even funny. If you want things done right and you need it done FAST, then we're your best bet.

- **Reference Architecture.** You'll get everything you need from the ground up built using 100% infrastructure as code.
- **Release Engineering.** You'll have end-to-end CI/CD with unlimited staging environments.
- **Site Reliability Engineering.** You'll have total visibility into your apps and microservices.
- **Security Baseline.** You'll have built-in governance with accountability and audit logs for all changes.
- **GitOps.** You'll be able to operate your infrastructure via Pull Requests.
- **Training.** You'll receive hands-on training so your team can operate what we build.
- **Questions.** You'll have a direct line of communication between our teams via a Shared Slack channel.
- **Troubleshooting.** You'll get help to triage when things aren't working.
- **Code Reviews.** You'll receive constructive feedback on Pull Requests.
- **Bug Fixes.** We'll rapidly work with you to fix any bugs in our projects.

## Slack Community

Join our [Open Source Community][slack] on Slack. It's **FREE** for everyone! Our "SweetOps" community is where you get to talk with others who share a similar vision for how to rollout and manage infrastructure. This is the best place to talk shop, ask questions, solicit feedback, and work together as a community to build totally *sweet* infrastructure.

## Discourse Forums

Participate in our [Discourse Forums][discourse]. Here you'll find answers to commonly asked questions. Most questions will be related to the enormous number of projects we support on our GitHub. Come here to collaborate on answers, find solutions, and get ideas about the products and services we value. It only takes a minute to get started! Just sign in with SSO using your GitHub account.

## Newsletter

Sign up for [our newsletter][newsletter] that covers everything on our technology radar.  Receive updates on what we're up to on GitHub as well as awesome new projects we discover.

## Office Hours

[Join us every Wednesday via Zoom][office_hours] for our weekly "Lunch & Learn" sessions. It's **FREE** for everyone!

[![zoom](https://img.cloudposse.com/fit-in/200x200/https://cloudposse.com/wp-content/uploads/2019/08/Powered-by-Zoom.png")][office_hours]

## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/cloudposse/atmos/issues) to report any bugs or file feature requests.

### Developing

If you are interested in being a contributor and want to get involved in developing this project or [help out](https://cpco.io/help-out) with our other projects, we would love to hear from you! Shoot us an [email][email].

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.

 1. **Fork** the repo on GitHub
 2. **Clone** the project to your own machine
 3. **Commit** changes to your own branch
 4. **Push** your work back up to your fork
 5. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!


## Copyright

Copyright © 2017-2020 [Cloud Posse, LLC](https://cpco.io/copyright)



## License

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

See [LICENSE](LICENSE) for full details.

```text
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
```









## Trademarks

All other trademarks referenced herein are the property of their respective owners.

## About

This project is maintained and funded by [Cloud Posse, LLC][website]. Like it? Please let us know by [leaving a testimonial][testimonial]!

[![Cloud Posse][logo]][website]

We're a [DevOps Professional Services][hire] company based in Los Angeles, CA. We ❤️  [Open Source Software][we_love_open_source].

We offer [paid support][commercial_support] on all of our projects.

Check out [our other projects][github], [follow us on twitter][twitter], [apply for a job][jobs], or [hire us][hire] to help with your cloud strategy and implementation.



### Contributors

|  [![Erik Osterman][osterman_avatar]][osterman_homepage]<br/>[Erik Osterman][osterman_homepage] | [![Andriy Knysh][aknysh_avatar]][aknysh_homepage]<br/>[Andriy Knysh][aknysh_homepage] |
|---|---|

  [osterman_homepage]: https://github.com/osterman
  [osterman_avatar]: https://img.cloudposse.com/150x150/https://github.com/osterman.png
  [aknysh_homepage]: https://github.com/aknysh
  [aknysh_avatar]: https://img.cloudposse.com/150x150/https://github.com/aknysh.png

[![README Footer][readme_footer_img]][readme_footer_link]
[![Beacon][beacon]][website]

  [logo]: https://cloudposse.com/logo-300x69.svg
  [docs]: https://cpco.io/docs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=docs
  [website]: https://cpco.io/homepage?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=website
  [github]: https://cpco.io/github?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=github
  [jobs]: https://cpco.io/jobs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=jobs
  [hire]: https://cpco.io/hire?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=hire
  [slack]: https://cpco.io/slack?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=slack
  [linkedin]: https://cpco.io/linkedin?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=linkedin
  [twitter]: https://cpco.io/twitter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=twitter
  [testimonial]: https://cpco.io/leave-testimonial?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=testimonial
  [office_hours]: https://cloudposse.com/office-hours?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=office_hours
  [newsletter]: https://cpco.io/newsletter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=newsletter
  [discourse]: https://ask.sweetops.com/?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=discourse
  [email]: https://cpco.io/email?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=email
  [commercial_support]: https://cpco.io/commercial-support?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=commercial_support
  [we_love_open_source]: https://cpco.io/we-love-open-source?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=we_love_open_source
  [terraform_modules]: https://cpco.io/terraform-modules?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=terraform_modules
  [readme_header_img]: https://cloudposse.com/readme/header/img
  [readme_header_link]: https://cloudposse.com/readme/header/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=readme_header_link
  [readme_footer_img]: https://cloudposse.com/readme/footer/img
  [readme_footer_link]: https://cloudposse.com/readme/footer/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=readme_footer_link
  [readme_commercial_support_img]: https://cloudposse.com/readme/commercial-support/img
  [readme_commercial_support_link]: https://cloudposse.com/readme/commercial-support/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/atmos&utm_content=readme_commercial_support_link
  [share_twitter]: https://twitter.com/intent/tweet/?text=atmos&url=https://github.com/cloudposse/atmos
  [share_linkedin]: https://www.linkedin.com/shareArticle?mini=true&title=atmos&url=https://github.com/cloudposse/atmos
  [share_reddit]: https://reddit.com/submit/?url=https://github.com/cloudposse/atmos
  [share_facebook]: https://facebook.com/sharer/sharer.php?u=https://github.com/cloudposse/atmos
  [share_googleplus]: https://plus.google.com/share?url=https://github.com/cloudposse/atmos
  [share_email]: mailto:?subject=atmos&body=https://github.com/cloudposse/atmos
  [beacon]: https://ga-beacon.cloudposse.com/UA-76589703-4/cloudposse/atmos?pixel&cs=github&cm=readme&an=atmos
