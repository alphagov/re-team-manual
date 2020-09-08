### 12. Deploying alertmanager to the new platform

Date: 2018-11-06

#### Status

Accepted

#### Context

The Observe team is a part of the new platform team, which is building out kubernetes capability in GDS.

There is a long-term goal that teams in GDS should avoid running bespoke infrastructure, specific to that team, so that any infrastructure we run is run in a common way and supportible by many people.

We also have a desire to migrate off ECS.  ECS is painful for running alertmanager because:

  - ECS does not support dropping configuration files in place
  - ECS does not support exposing multiple ports using load balancer for service discovery

Kubernetes does not have either of these limitations.

Currently, we have a plan to migrate everything to EC2, in order to get away from ECS.  We have quite a bit of outstanding pain from the old way of doing things:

  - we have two different deploy processes; one using the Makefile and one using the deploy_enclave.sh
  - we have two different code styles, related to the above
  - we have two different types of infrastructure

We have not fully planned out how we would migrate alertmanager to EC2, but we suspect it would involve at least the following tasks:

  - create a way of provisioning an EC2 instance with alertmanager installed (probably a stock ubuntu AMI with cloud.conf to install software)
  - create a way of deploying that instance with configuration added (probably a terraform module similar to what we have for prometheus)
  - actually deploy some alertmanagers to EC2 in parallel with ECS
  - migrate prometheus to start using both EC2 and ECS alertmanagers in parallel
  - once we're confident, switch off the ECS alertmanagers
  - tidy up the old ECS alertmanager code

This feels like a lot of work, especially if our longer-term goal is that we should not run bespoke infrastructure and should instead run in some common way such as the new platform.

Nevertheless, we could leave alertmanager in ECS but still ease some of the pain by refactoring the terraform code to be the new module-style instead of the old project-and-Makefile style, even if we leave alertmanager itself in ECS.

(Prometheus is different: we want to run prometheus the same way that non-PaaS teams such as Verify or Pay run it, so that we can offer guidance to them. The principle is the same: we want to run things the same way other GDS teams run them.)

#### Decision

1. We will pause any work migrating alertmanager to EC2
2. We will run an alertmanager in the new platform, leaving the remaining alertmanagers in ECS
3. We will try to migrate as much of nginx out of ECS as possible; in particular, we want paas-proxy to move to the same network (possibly same EC2 instance) as prometheus.
4. We will refactor our terraform for ECS to be module-based rather than the old project-and-Makefile style, so that we reduce the different types of code and deployment style.
5. We will keep prometheus running in EC2 and not migrate it to the new platform (although new platform environments will each have a prometheus available to them)

#### Consequences

We will have to be careful to keep alertmanager configuration in sync between the old and new infrascture.

We will have to keep our ECS instances running longer than we might otherwise choose to.
