<!-- This file was automatically generated by the `build-harness`. Make all changes to `README.yaml` and run `make readme` to rebuild this file. -->
[![README Header][readme_header_img]][readme_header_link]

[![Cloud Posse][logo]](https://cpco.io/homepage)

# terraform-aws-elastic-beanstalk-environment

 [![Codefresh Status](https://g.codefresh.io/api/badges/pipeline/cloudposse/terraform-modules%2Fterraform-aws-elastic-beanstalk-environment?type=cf-1)](https://g.codefresh.io/public/accounts/cloudposse/pipelines/5d182cb1ac440444a6c0082b) [![Latest Release](https://img.shields.io/github/release/cloudposse/terraform-aws-elastic-beanstalk-environment.svg)](https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment/releases/latest) [![Slack Community](https://slack.cloudposse.com/badge.svg)](https://slack.cloudposse.com)


Terraform module to provision AWS Elastic Beanstalk environment


---

This project is part of our comprehensive ["SweetOps"](https://cpco.io/sweetops) approach towards DevOps. 
[<img align="right" title="Share via Email" src="https://docs.cloudposse.com/images/ionicons/ios-email-outline-2.0.1-16x16-999999.svg"/>][share_email]
[<img align="right" title="Share on Google+" src="https://docs.cloudposse.com/images/ionicons/social-googleplus-outline-2.0.1-16x16-999999.svg" />][share_googleplus]
[<img align="right" title="Share on Facebook" src="https://docs.cloudposse.com/images/ionicons/social-facebook-outline-2.0.1-16x16-999999.svg" />][share_facebook]
[<img align="right" title="Share on Reddit" src="https://docs.cloudposse.com/images/ionicons/social-reddit-outline-2.0.1-16x16-999999.svg" />][share_reddit]
[<img align="right" title="Share on LinkedIn" src="https://docs.cloudposse.com/images/ionicons/social-linkedin-outline-2.0.1-16x16-999999.svg" />][share_linkedin]
[<img align="right" title="Share on Twitter" src="https://docs.cloudposse.com/images/ionicons/social-twitter-outline-2.0.1-16x16-999999.svg" />][share_twitter]


[![Terraform Open Source Modules](https://docs.cloudposse.com/images/terraform-open-source-modules.svg)][terraform_modules]



It's 100% Open Source and licensed under the [APACHE2](LICENSE).







We literally have [*hundreds of terraform modules*][terraform_modules] that are Open Source and well-maintained. Check them out! 







## Usage


**IMPORTANT:** The `master` branch is used in `source` just as an example. In your code, do not pin to `master` because there may be breaking changes between releases.
Instead pin to the release tag (e.g. `?ref=tags/x.y.z`) of one of our [latest releases](https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment/releases).



For a complete example, see [examples/complete](examples/complete)

```hcl
  provider "aws" {
    region = var.region
  }

  module "vpc" {
    source     = "git::https://github.com/cloudposse/terraform-aws-vpc.git?ref=tags/0.8.0"
    namespace  = var.namespace
    stage      = var.stage
    name       = var.name
    cidr_block = "172.16.0.0/16"
  }

  module "subnets" {
    source               = "git::https://github.com/cloudposse/terraform-aws-dynamic-subnets.git?ref=tags/0.16.0"
    availability_zones   = var.availability_zones
    namespace            = var.namespace
    stage                = var.stage
    name                 = var.name
    vpc_id               = module.vpc.vpc_id
    igw_id               = module.vpc.igw_id
    cidr_block           = module.vpc.vpc_cidr_block
    nat_gateway_enabled  = true
    nat_instance_enabled = false
  }

  module "elastic_beanstalk_application" {
    source      = "git::https://github.com/cloudposse/terraform-aws-elastic-beanstalk-application.git?ref=tags/0.3.0"
    namespace   = var.namespace
    stage       = var.stage
    name        = var.name
    description = "Test elastic_beanstalk_application"
  }

  module "elastic_beanstalk_environment" {
    source                             = "git::https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment.git?ref=master"
    namespace                          = var.namespace
    stage                              = var.stage
    name                               = var.name
    description                        = "Test elastic_beanstalk_environment"
    region                             = var.region
    availability_zone_selector         = "Any 2"
    dns_zone_id                        = var.dns_zone_id
    elastic_beanstalk_application_name = module.elastic_beanstalk_application.elastic_beanstalk_application_name

    instance_type           = "t3.small"
    autoscale_min           = 1
    autoscale_max           = 2
    updating_min_in_service = 0
    updating_max_batch      = 1

    loadbalancer_type       = "application"
    vpc_id                  = module.vpc.vpc_id
    loadbalancer_subnets    = module.subnets.public_subnet_ids
    application_subnets     = module.subnets.private_subnet_ids
    allowed_security_groups = [module.vpc.vpc_default_security_group_id]

    // https://docs.aws.amazon.com/elasticbeanstalk/latest/platforms/platforms-supported.html
    // https://docs.aws.amazon.com/elasticbeanstalk/latest/platforms/platforms-supported.html#platforms-supported.docker
    solution_stack_name = "64bit Amazon Linux 2018.03 v2.12.17 running Docker 18.06.1-ce"

    additional_settings = [
      {
        namespace = "aws:elasticbeanstalk:application:environment"
        name      = "DB_HOST"
        value     = "xxxxxxxxxxxxxx"
      },
      {
        namespace = "aws:elasticbeanstalk:application:environment"
        name      = "DB_USERNAME"
        value     = "yyyyyyyyyyyyy"
      },
      {
        namespace = "aws:elasticbeanstalk:application:environment"
        name      = "DB_PASSWORD"
        value     = "zzzzzzzzzzzzzzzzzzz"
      },
      {
        namespace = "aws:elasticbeanstalk:application:environment"
        name      = "ANOTHER_ENV_VAR"
        value     = "123456789"
      }
    ]
  }
}
```






## Makefile Targets
```
Available targets:

  help                                Help screen
  help/all                            Display help for all targets
  help/short                          This help short screen
  lint                                Lint terraform code

```
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| additional_settings | Additional Elastic Beanstalk setttings. For full list of options, see https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/command-options-general.html | object | `<list>` | no |
| alb_zone_id | ALB zone id | map(string) | `<map>` | no |
| allowed_security_groups | List of security groups to be allowed to connect to the EC2 instances | list(string) | `<list>` | no |
| application_port | Port application is listening on | number | `80` | no |
| application_subnets | List of subnets to place EC2 instances | list(string) | - | yes |
| associate_public_ip_address | Whether to associate public IP addresses to the instances | bool | `false` | no |
| attributes | Additional attributes (e.g. `1`) | list(string) | `<list>` | no |
| autoscale_lower_bound | Minimum level of autoscale metric to remove an instance | number | `20` | no |
| autoscale_lower_increment | How many Amazon EC2 instances to remove when performing a scaling activity. | number | `-1` | no |
| autoscale_max | Maximum instances to launch | number | `3` | no |
| autoscale_measure_name | Metric used for your Auto Scaling trigger | string | `CPUUtilization` | no |
| autoscale_min | Minumum instances to launch | number | `2` | no |
| autoscale_statistic | Statistic the trigger should use, such as Average | string | `Average` | no |
| autoscale_unit | Unit for the trigger measurement, such as Bytes | string | `Percent` | no |
| autoscale_upper_bound | Maximum level of autoscale metric to add an instance | number | `80` | no |
| autoscale_upper_increment | How many Amazon EC2 instances to add when performing a scaling activity | number | `1` | no |
| availability_zone_selector | Availability Zone selector | string | `Any 2` | no |
| delimiter | Delimiter to be used between `name`, `namespace`, `stage`, etc. | string | `-` | no |
| description | Short description of the Environment | string | `` | no |
| dns_zone_id | Route53 parent zone ID. The module will create sub-domain DNS record in the parent zone for the EB environment | string | `` | no |
| elastic_beanstalk_application_name | Elastic Beanstalk application name | string | - | yes |
| elb_scheme | Specify `internal` if you want to create an internal load balancer in your Amazon VPC so that your Elastic Beanstalk application cannot be accessed from outside your Amazon VPC | string | `public` | no |
| enable_log_publication_control | Copy the log files for your application's Amazon EC2 instances to the Amazon S3 bucket associated with your application | bool | `false` | no |
| enable_stream_logs | Whether to create groups in CloudWatch Logs for proxy and deployment logs, and stream logs from each instance in your environment | bool | `false` | no |
| enhanced_reporting_enabled | Whether to enable "enhanced" health reporting for this environment.  If false, "basic" reporting is used.  When you set this to false, you must also set `enable_managed_actions` to false | bool | `true` | no |
| env_vars | Map of custom ENV variables to be provided to the application running on Elastic Beanstalk, e.g. env_vars = { DB_USER = 'admin' DB_PASS = 'xxxxxx' } | map(string) | `<map>` | no |
| environment_type | Environment type, e.g. 'LoadBalanced' or 'SingleInstance'.  If setting to 'SingleInstance', `rolling_update_type` must be set to 'Time', `updating_min_in_service` must be set to 0, and `loadbalancer_subnets` will be unused (it applies to the ELB, which does not exist in SingleInstance environments) | string | `LoadBalanced` | no |
| force_destroy | Force destroy the S3 bucket for load balancer logs | bool | `false` | no |
| health_streaming_delete_on_terminate | Whether to delete the log group when the environment is terminated. If false, the health data is kept RetentionInDays days. | bool | `false` | no |
| health_streaming_enabled | For environments with enhanced health reporting enabled, whether to create a group in CloudWatch Logs for environment health and archive Elastic Beanstalk environment health data. For information about enabling enhanced health, see aws:elasticbeanstalk:healthreporting:system. | bool | `false` | no |
| health_streaming_retention_in_days | The number of days to keep the archived health data before it expires. | number | `7` | no |
| healthcheck_url | Application Health Check URL. Elastic Beanstalk will call this URL to check the health of the application running on EC2 instances | string | `/healthcheck` | no |
| http_listener_enabled | Enable port 80 (http) | bool | `true` | no |
| instance_refresh_enabled | Enable weekly instance replacement. | bool | `true` | no |
| instance_type | Instances type | string | `t2.micro` | no |
| keypair | Name of SSH key that will be deployed on Elastic Beanstalk and DataPipeline instance. The key should be present in AWS | string | `` | no |
| loadbalancer_certificate_arn | Load Balancer SSL certificate ARN. The certificate must be present in AWS Certificate Manager | string | `` | no |
| loadbalancer_managed_security_group | Load balancer managed security group | string | `` | no |
| loadbalancer_security_groups | Load balancer security groups | list(string) | `<list>` | no |
| loadbalancer_ssl_policy | Specify a security policy to apply to the listener. This option is only applicable to environments with an application load balancer | string | `` | no |
| loadbalancer_subnets | List of subnets to place Elastic Load Balancer | list(string) | `<list>` | no |
| loadbalancer_type | Load Balancer type, e.g. 'application' or 'classic' | string | `classic` | no |
| logs_delete_on_terminate | Whether to delete the log groups when the environment is terminated. If false, the logs are kept RetentionInDays days | bool | `false` | no |
| logs_retention_in_days | The number of days to keep log events before they expire. | number | `7` | no |
| managed_actions_enabled | Enable managed platform updates. When you set this to true, you must also specify a `PreferredStartTime` and `UpdateLevel` | bool | `true` | no |
| name | Solution name, e.g. 'app' or 'cluster' | string | - | yes |
| namespace | Namespace, which could be your organization name, e.g. 'eg' or 'cp' | string | `` | no |
| preferred_start_time | Configure a maintenance window for managed actions in UTC | string | `Sun:10:00` | no |
| region | AWS region | string | - | yes |
| rolling_update_enabled | Whether to enable rolling update | bool | `true` | no |
| rolling_update_type | `Health` or `Immutable`. Set it to `Immutable` to apply the configuration change to a fresh group of instances | string | `Health` | no |
| root_volume_size | The size of the EBS root volume | number | `8` | no |
| root_volume_type | The type of the EBS root volume | string | `gp2` | no |
| solution_stack_name | Elastic Beanstalk stack, e.g. Docker, Go, Node, Java, IIS. For more info, see https://docs.aws.amazon.com/elasticbeanstalk/latest/platforms/platforms-supported.html | string | - | yes |
| ssh_listener_enabled | Enable SSH port | bool | `false` | no |
| ssh_listener_port | SSH port | number | `22` | no |
| ssh_source_restriction | Used to lock down SSH access to the EC2 instances | string | `0.0.0.0/0` | no |
| stage | Stage, e.g. 'prod', 'staging', 'dev', or 'test' | string | `` | no |
| tags | Additional tags (e.g. `map('BusinessUnit`,`XYZ`) | map(string) | `<map>` | no |
| tier | Elastic Beanstalk Environment tier, 'WebServer' or 'Worker' | string | `WebServer` | no |
| update_level | The highest level of update to apply with managed platform updates | string | `minor` | no |
| updating_max_batch | Maximum number of instances to update at once | number | `1` | no |
| updating_min_in_service | Minimum number of instances in service during update | number | `1` | no |
| version_label | Elastic Beanstalk Application version to deploy | string | `` | no |
| vpc_id | ID of the VPC in which to provision the AWS resources | string | - | yes |
| wait_for_ready_timeout | The maximum duration to wait for the Elastic Beanstalk Environment to be in a ready state before timing out | string | `20m` | no |

## Outputs

| Name | Description |
|------|-------------|
| all_settings | List of all option settings configured in the environment. These are a combination of default settings and their overrides from setting in the configuration |
| application | The Elastic Beanstalk Application specified for this environment |
| autoscaling_groups | The autoscaling groups used by this environment |
| ec2_instance_profile_role_name | Instance IAM role name |
| elb_zone_id | ELB zone id |
| endpoint | Fully qualified DNS name for the environment |
| hostname | DNS hostname |
| id | ID of the Elastic Beanstalk environment |
| instances | Instances used by this environment |
| launch_configurations | Launch configurations in use by this environment |
| load_balancers | Elastic Load Balancers in use by this environment |
| name | Name |
| queues | SQS queues in use by this environment |
| security_group_id | Security group id |
| setting | Settings specifically set for this environment |
| tier | The environment tier |
| triggers | Autoscaling triggers in use by this environment |




## Share the Love 

Like this project? Please give it a ★ on [our GitHub](https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment)! (it helps us **a lot**) 

Are you using this project or any of our other projects? Consider [leaving a testimonial][testimonial]. =)


## Related Projects

Check out these related projects.

- [terraform-aws-jenkins](https://github.com/cloudposse/terraform-aws-jenkins) - Terraform module to build Docker image with Jenkins, save it to an ECR repo, and deploy to Elastic Beanstalk running Docker stack
- [terraform-aws-elastic-beanstalk-application](https://github.com/cloudposse/terraform-aws-elastic-beanstalk-application) - Terraform Module to define an ElasticBeanstalk Application
- [geodesic](https://github.com/cloudposse/geodesic) -  Geodesic is the fastest way to get up and running with a rock solid, production grade cloud platform built on strictly Open Source tools.
- [terraform-aws-elasticache-cloudwatch-sns-alarms](https://github.com/cloudposse/terraform-aws-elasticache-cloudwatch-sns-alarms) -  Terraform module that configures CloudWatch SNS alerts for ElastiCache



## Help

**Got a question?**

File a GitHub [issue](https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment/issues), send us an [email][email] or join our [Slack Community][slack].

[![README Commercial Support][readme_commercial_support_img]][readme_commercial_support_link]

## Commercial Support

Work directly with our team of DevOps experts via email, slack, and video conferencing. 

We provide [*commercial support*][commercial_support] for all of our [Open Source][github] projects. As a *Dedicated Support* customer, you have access to our team of subject matter experts at a fraction of the cost of a full-time engineer. 

[![E-Mail](https://img.shields.io/badge/email-hello@cloudposse.com-blue.svg)][email]

- **Questions.** We'll use a Shared Slack channel between your team and ours.
- **Troubleshooting.** We'll help you triage why things aren't working.
- **Code Reviews.** We'll review your Pull Requests and provide constructive feedback.
- **Bug Fixes.** We'll rapidly work to fix any bugs in our projects.
- **Build New Terraform Modules.** We'll [develop original modules][module_development] to provision infrastructure.
- **Cloud Architecture.** We'll assist with your cloud strategy and design.
- **Implementation.** We'll provide hands-on support to implement our reference architectures. 



## Terraform Module Development

Are you interested in custom Terraform module development? Submit your inquiry using [our form][module_development] today and we'll get back to you ASAP.


## Slack Community

Join our [Open Source Community][slack] on Slack. It's **FREE** for everyone! Our "SweetOps" community is where you get to talk with others who share a similar vision for how to rollout and manage infrastructure. This is the best place to talk shop, ask questions, solicit feedback, and work together as a community to build totally *sweet* infrastructure.

## Newsletter

Signup for [our newsletter][newsletter] that covers everything on our technology radar.  Receive updates on what we're up to on GitHub as well as awesome new projects we discover. 

## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment/issues) to report any bugs or file feature requests.

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

Copyright © 2017-2019 [Cloud Posse, LLC](https://cpco.io/copyright)



## License 

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 

See [LICENSE](LICENSE) for full details.

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









## Trademarks

All other trademarks referenced herein are the property of their respective owners.

## About

This project is maintained and funded by [Cloud Posse, LLC][website]. Like it? Please let us know by [leaving a testimonial][testimonial]!

[![Cloud Posse][logo]][website]

We're a [DevOps Professional Services][hire] company based in Los Angeles, CA. We ❤️  [Open Source Software][we_love_open_source].

We offer [paid support][commercial_support] on all of our projects.  

Check out [our other projects][github], [follow us on twitter][twitter], [apply for a job][jobs], or [hire us][hire] to help with your cloud strategy and implementation.



### Contributors

|  [![Erik Osterman][osterman_avatar]][osterman_homepage]<br/>[Erik Osterman][osterman_homepage] | [![Igor Rodionov][goruha_avatar]][goruha_homepage]<br/>[Igor Rodionov][goruha_homepage] | [![Andriy Knysh][aknysh_avatar]][aknysh_homepage]<br/>[Andriy Knysh][aknysh_homepage] | [![Guillaume Delacour][guikcd_avatar]][guikcd_homepage]<br/>[Guillaume Delacour][guikcd_homepage] | [![Viktor Erpylev][velmoga_avatar]][velmoga_homepage]<br/>[Viktor Erpylev][velmoga_homepage] | [![Lucas Pearson][pearson-lucas-dev_avatar]][pearson-lucas-dev_homepage]<br/>[Lucas Pearson][pearson-lucas-dev_homepage] | [![Chris Green][DirectRoot_avatar]][DirectRoot_homepage]<br/>[Chris Green][DirectRoot_homepage] |
|---|---|---|---|---|---|---|


  [osterman_homepage]: https://github.com/osterman
  [osterman_avatar]: http://s.gravatar.com/avatar/88c480d4f73b813904e00a5695a454cb?s=144


  [goruha_homepage]: https://github.com/goruha/
  [goruha_avatar]: http://s.gravatar.com/avatar/bc70834d32ed4517568a1feb0b9be7e2?s=144


  [aknysh_homepage]: https://github.com/aknysh/
  [aknysh_avatar]: https://avatars0.githubusercontent.com/u/7356997?v=4&u=ed9ce1c9151d552d985bdf5546772e14ef7ab617&s=144

  [guikcd_homepage]: https://github.com/guikcd
  [guikcd_avatar]: https://img.cloudposse.com/150x150/https://github.com/guikcd.png
  [velmoga_homepage]: https://github.com/velmoga
  [velmoga_avatar]: https://img.cloudposse.com/150x150/https://github.com/velmoga.png
  [pearson-lucas-dev_homepage]: https://github.com/pearson-lucas-dev
  [pearson-lucas-dev_avatar]: https://img.cloudposse.com/150x150/https://github.com/pearson-lucas-dev.png
  [DirectRoot_homepage]: https://github.com/DirectRoot
  [DirectRoot_avatar]: https://img.cloudposse.com/150x150/https://github.com/DirectRoot.png



[![README Footer][readme_footer_img]][readme_footer_link]
[![Beacon][beacon]][website]

  [logo]: https://cloudposse.com/logo-300x69.svg
  [docs]: https://cpco.io/docs
  [website]: https://cpco.io/homepage
  [github]: https://cpco.io/github
  [jobs]: https://cpco.io/jobs
  [hire]: https://cpco.io/hire
  [slack]: https://cpco.io/slack
  [linkedin]: https://cpco.io/linkedin
  [twitter]: https://cpco.io/twitter
  [testimonial]: https://cpco.io/leave-testimonial
  [newsletter]: https://cpco.io/newsletter
  [email]: https://cpco.io/email
  [commercial_support]: https://cpco.io/commercial-support
  [we_love_open_source]: https://cpco.io/we-love-open-source
  [module_development]: https://cpco.io/module-development
  [terraform_modules]: https://cpco.io/terraform-modules
  [readme_header_img]: https://cloudposse.com/readme/header/img?repo=cloudposse/terraform-aws-elastic-beanstalk-environment
  [readme_header_link]: https://cloudposse.com/readme/header/link?repo=cloudposse/terraform-aws-elastic-beanstalk-environment
  [readme_footer_img]: https://cloudposse.com/readme/footer/img?repo=cloudposse/terraform-aws-elastic-beanstalk-environment
  [readme_footer_link]: https://cloudposse.com/readme/footer/link?repo=cloudposse/terraform-aws-elastic-beanstalk-environment
  [readme_commercial_support_img]: https://cloudposse.com/readme/commercial-support/img?repo=cloudposse/terraform-aws-elastic-beanstalk-environment
  [readme_commercial_support_link]: https://cloudposse.com/readme/commercial-support/link?repo=cloudposse/terraform-aws-elastic-beanstalk-environment
  [share_twitter]: https://twitter.com/intent/tweet/?text=terraform-aws-elastic-beanstalk-environment&url=https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment
  [share_linkedin]: https://www.linkedin.com/shareArticle?mini=true&title=terraform-aws-elastic-beanstalk-environment&url=https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment
  [share_reddit]: https://reddit.com/submit/?url=https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment
  [share_facebook]: https://facebook.com/sharer/sharer.php?u=https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment
  [share_googleplus]: https://plus.google.com/share?url=https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment
  [share_email]: mailto:?subject=terraform-aws-elastic-beanstalk-environment&body=https://github.com/cloudposse/terraform-aws-elastic-beanstalk-environment
  [beacon]: https://ga-beacon.cloudposse.com/UA-76589703-4/cloudposse/terraform-aws-elastic-beanstalk-environment?pixel&cs=github&cm=readme&an=terraform-aws-elastic-beanstalk-environment
