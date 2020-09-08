### 2. Configuration Management

Date: 2018-04-18

#### Status

No longer relevant.

This decision related to the alpha code (in
alphagov/prometheus-aws-configuration), which is no longer being
actively developed.

#### Context

We have the requirement of adding some resources to the base cloud instances. We currently do
this using the [cloud.conf](https://github.com/alphagov/prometheus-aws-configuration/blob/375f34600e373aa0e4c66fcae032ceee361d8c21/terraform/modules/prometheus/cloud.conf) system. This presents us with some limitations, such as configuration
being limited to 16kb, duplication in each instance terraform and a lack of fast feedback testing.

#### Decision

We have decided to move away from cloud.conf as much as possible and instead use it to instantiate
a masterless puppet agent which will manage the resources.

#### Consequences

This change firstly brings us inline with the GDS Way, and most of the programs, in our selection of
tooling. It removes the limit of 16kb of configuration and allows the reuse of existing testing tools.
By running in masterless mode we remove the need of running a puppet master and related infrastructure.
Our puppet manifests can be reused both within tools and possibly other programs.

It's worth noting we will still need a basic cloud.conf file to install and run the puppet agent but this
will be minimal and reusable in each of our terraform projects. There is also the risk that people will
put more in the puppet code than they should. This will be remediated by review of
architecture and code.
