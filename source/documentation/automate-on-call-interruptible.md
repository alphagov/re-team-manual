This document will serve as a quick reference for people on the Automate
interruptible or on-call rotas. It often links to external resources for more
detail and context.

## Interruptible routines

A few things to do as part of the interruptible routine:

1. Update the document that is linked from the #re-autom8 channel topic with
   that week's (or day's) interruptible person
1. Update the topic in #reliabily-eng with that week's (or day's) interruptible
   person

## GOV.UK Verify

Team manual:
[https://verify-team-manual.cloudapps.digital/](https://verify-team-manual.cloudapps.digital/)

### Hub

#### On call

The only alert which can directly call the automate interruptible out
of hours is the Prometheus Cronitor heartbeat for the hub production
environment.  This is an alert which always fires; we use Cronitor to
create a PagerDuty incident if the alert ever fails.  In this way we
have confidence that the alerting system is functioning correctly.

Other than that, only escalations from the Verify primary on-call team
apply out of hours; Alertmanager won't directly page reliability
engineering. On occasion it may also be the result of a cyber security
escalation.

[Concourse](https://cd.gds-reliability.engineering/teams/verify/pipelines/deploy-verify-hub)
deploys the Hub to ECS (one cluster per application) on Fargate (i.e. not ECS on
EC2). Reliability engineering only offer out-of-hours support for the
production services. Some useful links:

* [verify-infrastructure
  repository](https://github.com/alphagov/verify-infrastructure)
* [verify-infrastructure-config
  repository](https://github.com/alphagov/verify-infrastructure-config)
* [verify-terraform repository](https://github.com/alphagov/verify-terraform)
* [environment
  links](https://verify-team-manual.cloudapps.digital/documentation/support/second-line/environment-links.html)

Relevant gds-cli aws account names:

* verify-prod-a
* verify-tools

#### Interruptible

Reliability engineers may also need to respond in hours to issues and incidents
in non-production environments. Examples:

* problems deploying the application to non-production environments (such as
  integration or staging)
* machine reboots (prometheus only; all other non-tools environment machines are
  routinely rebuilt by
  [concourse](https://cd.gds-reliability.engineering/teams/verify/pipelines/deploy-verify-hub))

Some useful links:

* [Verify concourse team](https://cd.gds-reliability.engineering/?search=team%3A%20verify)
* [smoke
  tests](https://cd.gds-reliability.engineering/teams/verify/pipelines/smoketests)

Relevant gds-cli aws account names:

* verify-staging
* verify-integration-a

### DCS

#### On call

The Document Checking Service runs in GSP on AWS. As with Hub, reliability
engineering are not part of the primary on call flow and so will only be called
on if necessary as part of an escalation.

Some useful links:

* [doc-checking repository](https://github.com/alphagov/doc-checking)
* [DCS grafana dashboard](https://grafana.london.verify.govsvc.uk/d/f598d65d-85a5-4880-96cf-98659686058a/dcs-production?orgId=1&refresh=1m&from=1581437875220&to=1582042675220)
* [DCS Cyber Security playbooks](https://github.com/alphagov/cyber-security-monitoring-playbooks/tree/master/verify-dcs/)
* [Additional DCS support docs](https://verify-team-manual.cloudapps.digital/documentation/support/#dcs)

#### Interruptible

The most common tasks are general escalations from the Verify yak team or the Verify devs.

### Proxy Node

#### On call

The proxy node is part of the eIDAS framework. It is deployed to the GSP on the
Verify cluster by the [in-cluster
concourse](https://ci.london.verify.govsvc.uk/teams/proxy-node-prod/pipelines/deploy).

At the time of writing, the proxy node is not supported out of hours, except for
one scenario: a security issue severe enough to warrant the invocation of the
[kill
switch](https://ci.london.verify.govsvc.uk/teams/proxy-node-prod/pipelines/killswitch).

### GSP Platform

The GSP platform supports DCS and the Proxy Node above, but there are
some alerts for the platform itself.

#### On call

The only alert which can directly call the automate interruptible out
of hours is the Prometheus Cronitor heartbeat for the GSP cluster.
This is an alert which always fires; we use Cronitor to create a
PagerDuty incident if the alert ever fails.  In this way we have
confidence that the alerting system is functioning correctly.

### Dev infrastructure

#### Interruptible

Other "misc" infrastructure for Verify is supported in-hours:

* AWS tools environment which includes concourse, motomo, dash etc.

Relevant gds-cli aws account names:

* verify-tools

## Observe

### Alertmanager

#### On call

The Observe Alertmanager deployment is used by several programme's services to
route alerts to other systems like PagerDuty. It is part of Observe's Prometheus
service. Alertmanager is deployed to ECS Fargate by
[concourse](https://cd.gds-reliability.engineering/teams/autom8/pipelines/prometheus).
Alertmanager instances are clustered together to handle (among other things)
deduplication of downstream alerting (each prometheus instance sends alerts to
all the Alertmanager instances).

The most probable source of any kind of event will be cronitor.

* [alertmanager](https://alerts.monitoring.gds-reliability.engineering/) (This is a DNS round-robin record; ie there are multiple A records for multiple alertmanagers)
* [prometheus-aws-configuration-beta
  repository](https://github.com/alphagov/prometheus-aws-configuration-beta)
* [main
  article](https://re-team-manual.cloudapps.digital/prometheus-for-gds-paas-users.html)

Relevant gds-cli account names:

* re-prom-prod

#### Interruptible

In addition to Alertmanager, the Prometheus instances need to be managed
in-hours. The most common incident involving the Oberve prometheus instances is
related to the PaaS service broker not removing deleted PaaS apps from its S3
store, causing prometheus to alert on instances being down.

Main article: [Prometheus for GDS PaaS
Service](https://re-team-manual.cloudapps.digital/prometheus-for-gds-paas-users.html)

Useful links:

* [re-secrets repository](https://github.com/alphagov/re-secrets)
* [service broker repository](https://github.com/alphagov/cf_app_discovery)
* [grafana](https://grafana-paas.cloudapps.digital/)

Relevant gds-cli account names:

* re-prom-staging

## Cyber security escalations

### On call

Cyber security may escalate to reliability engineering out of hours for a number
of reasons. Possible candidates:

* proxy node or connector metadata CloudHSM communications look suspicious
* certain AWS account access out-of-hours (such as admin)
* some GSP cluster access patterns out-of-hours

Useful links:

* [GSP documentation](https://github.com/alphagov/gsp/tree/master/docs)

Relevant gds-cli targets:

* gsp-verify (for AWS)
* verify (for `kubectl` & other GSP-related actions)

#### Interruptible

TODO

## Performance Platform

Performance Platform is supported best effort, in-hours only.

### Interruptible

Main article: [Performance
Platform](https://re-team-manual.cloudapps.digital/performance-platform.html)

## Multi-tenant concourse

Terraform applied manually from the
[tech-ops-private](https://github.com/alphagov/tech-ops-private/tree/master/reliability-engineering/terraform/deployments/gds-tech-ops/cd)
repository.

Useful links:

* [Multi-tenant concourse](https://cd.gds-reliability.engineering/)
* [Concourse grafana](https://grafana.monitoring.cd.gds-reliability.engineering/)
* [Concourse prometheus](https://prom-1.monitoring.cd.gds-reliability.engineering/)
* [tech-ops repository](https://github.com/alphagov/tech-ops)
* [tech-ops-private repository](https://github.com/alphagov/tech-ops-private)

Relevant gds-cli account names:

* techops

### Terraform

When applying the multi-tenant concourse terraform (from `tech-ops-private` `reliability-engineering/terraform/deployments/gds-tech-ops/cd`), you may find Terraform:

* re-orders permissions lists
* replaces various AMIs
* replaces Prometheis

These should be fine. The AMI changes should roll out when the main team's roll-instances pipeline jobs run on the next weekday morning.

### Interruptible

#### Troubleshooting pending builds

Sometimes a team's pipeline will have jobs get stuck in a pending state and not run.

You can check that their workers are working okay by running other jobs (e.g. the info pipeline should be working). If this does not work, you may want to check that the team has healthy workers, you can see whether a worker is stalled or running by looking at `fly -t cd-main workers`. (`main` in this command can be replaced with any other team name, it is only used to find the correct Concourse instance, the team does not matter, all teams workers will be shown).

Otherwise, the cause of the issue might be [an upstream bug](https://github.com/concourse/concourse/issues/844#issuecomment-416745367). The normal workaround for this seems to be deleting (`fly -t cd-team-name destroy-pipeline -p bad-pipeline`) and re-creating (`fly -t cd-team-name set-pipeline -p bad-pipeline -c pipeline.yml`) the pipeline - but note it may be advisable to run set-pipeline first to look at the diff you'll be creating when you set the pipeline again - it may be necessary to set variables, and pipelines that self-update should contain some clues as to where to get those variables from. This process should be performable by the affected team.

#### Secrets over-requesting

Even with caching enabled, concourse can make heavy use of its secrets backend, in our
case AWS SSM Parameter Store. Parameter Store with its default settings has a rate limit
of 40 transactions per second. Occasionally concourse usage surges may hit this limit,
resulting in build failures with concourse complaining it's unable to interpolate a task
config. Note this will only happen after it has already retried a number of times. As a
temporary fix it's possible to [increase our account's rate limit allowance](https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-throughput.html#parameter-store-throughput-increasing-cli)
to 100 transactions per second, though last time we did this it cost us ~$10 a day, so we
try to avoid that.

A [splunk dashboard](https://gds.splunkcloud.com/en-GB/app/gds-010-techops/concourse_secrets)
exists to monitor teams secrets usage.

The way concourse's SSM secrets backend currently works, there are a few known situations
where secrets usage gets artificially amplified - in particular, repeated accesses to
non-existent secrets. So it's sometimes a good idea to keep an eye on these and make
teams aware that they might have a broken pipeline that's causing us problems.

#### Creating a new team

These are requested by PR in the `tech-ops-private` repository, which is available to everyone in the alphagov GitHub organisation:

* First, append to the **end** of the list of teams in `reliability-engineering/terraform/deployments/gds-tech-ops/cd/site.tf`
  Do not add part way through the list as Terraform may decide to start re-creating other roles which could cause an incident.
* Create a new file forked from `reliability-engineering/terraform/deployments/gds-tech-ops/cd/team-autom8.tf`
    * Replace all the references to `autom8` with the name of the new team
    * Replace the `re-autom8` owner team with as many new owner GitHub teams as required, but remember to leave `re-common-cloud` on the `viewers` list and `re-cd-temporary-owners` on the `owners` list.
    * In addition to `owners` and `members`, `pipeline_operators` and `viewers` may be specified.
    * Set the `desired_capacity` attribute of the `concourse-worker-pool` module as appropriate, and optionally set the `instance_type` attribute too (default is `t3.small`).
    * By default every team's workers runs in the same subnet and gets the same egress IP. Where it is a requirement that egress IPs be used only by a specific team, that team may be given its own subnet and NAT Gateway, as is done for e.g. the `gsp` team. We do not do this purely for AWS Role SourceIp Conditions, as the Principal will already be unique to the right team's worker group. They may be provided for cases like SSH. These cost more money.

Then when reviewing such requests, ensure they are sized appropriately and don't include a new subnet where they don't need to. Ensure the permissions are set up correctly and the requesting team knows the implications of the permissions chosen. It will be continuously deployed by the [deploy](https://cd.gds-reliability.engineering/teams/main/pipelines/deploy) pipeline, after passing through the staging deployment.

#### Redacting build logs

If someone runs a build on concourse that outputs a bunch of sensitive information, this will then end up being saved in concourse's build logs. And this may be considered and incident worth acting on.

In most cases the best action is to simply delete the pipeline with `fly destroy-pipeline` and re-create it. This can be done by the pipeline owner themselves.

If the task was launched using `fly execute` however, this will not work and the logs will have to be redacted manually. As there is not (currently) an in-built way of deleting a specific build's logs, you will have to remove the offending data by hand from concourse's underlying postgresql database where it is stored.

The recommended way of accessing this database is through connecting to one of the `concourse-web` instances via the AWS console and running `psql` using the credentials found in the file `/etc/systemd/system/concourse-web.service`. Once in the database, use the `builds` table to find the `build_id` of the target build (this is _not_ the same thing as "build #" as presented to the user). One way to do this is to deduce it from the `builds.create_time` field. The build logs themselves can be found as rows in tables named e.g. `pipeline_build_events_*`. Once you've found the relevant table(s), these rows should be safely deletable using a command such as `DELETE FROM pipeline_build_events_33 WHERE build_id=144160;`.

#### Escalating permissions

Certain people are authorised to, when necessary, assume `owner` level permissions in all teams. To trigger this:
* Browse to https://cd.gds-reliability.engineering/teams/main/pipelines/temporary-owners-promoter
* Select `promote-<username>` (if there is not one for your username, you are not authorised to do this, or the `update-pipeline` job hasn't run since you were given that permission).
* Trigger Build using the usual plus button in the top right.
* You may now need to log out of Concourse and log back in. You may need to repeat this process with fly.
* When you are done, either trigger a build of `demote-<username>`, or `demote-all-owners`. If you forget, `demote-all-owners` runs daily anyway.

## gds-users

### Interruptible

Please see [this Google Doc](https://docs.google.com/document/d/1nMTYFMl9Ffd1IKHLL8fOzzpstzX-xaP1v82NNe31_k4/edit) for information about this.

## AWS Account Actions

### Interruptible

Main article: [GDS AWS Account Management
Service](https://re-team-manual.cloudapps.digital/gds-aws-account-management-service.html)

### Deleting AWS Accounts no longer required

If the account uses the `aws-root-accounts@digital.cabinet-office.gov.uk` email address, then GDS staff who have access to the safe and membership of the `aws-root-accounts` google group can remove the account.

If it uses a different email address, the account can only be removed by someone with access to that email address and associated MFA secret.
