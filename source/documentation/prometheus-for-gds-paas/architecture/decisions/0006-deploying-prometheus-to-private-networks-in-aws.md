### 6. Model for deploying prometheus to private networks in AWS

Date: 2018-07-26

#### Status

Draft

#### Context

We are looking to offer prometheus to non-PaaS teams.  The
infrastructure to be monitored will be run by another team (called
"client team" in this document), but we will provide one or more
prometheus servers which will will be responsible for gathering
metrics from the underlying infrastructure.

Longer term, we are aiming to provide prometheus as a service to
multiple environments across multiple programmes.

##### Non-suitability of existing infrastructure

Our existing prometheus infrastructure (for PaaS teams) works by using
our [service broker][] to generate file_sd_configs which prometheus
then uses to scrape PaaS apps over the public internet.

This approach will not work for non-PaaS teams, because it's based on an
assumption – that every app is directly routable from the public
internet – that does not hold in non-PaaS environments.  Instead, apps
live on private networks and public access is controlled by firewalls
and load balancers.

[service broker]: https://github.com/alphagov/cf_app_discovery

##### Main problem to be solved: scraping apps on private networks

As the previous section explained, our main problem is that we want a
prometheus (provided by us) to be able to scrape apps and other
endpoints (owned by the client team, and living on a private network).
We want to do this in a way which does not require clients to
unnecessarily expose metrics endpoints to the public internet.

Some other things we would like to be able to do are:

 - maintain prometheus at a single common version, by upgrading
   prometheus across our whole estate
 - update prometheus configuration without having to restart
   prometheus
 - allow client teams to provide configuration (for example, for alert
   rules)
 - perform [ec2 service discovery][] by querying the EC2 API (for
   which we need permissions to read client account EC2 resources)

There are several ways we might provide a prometheus pattern that
allows us to scrape private endpoints:

 - provide an artefact to be deployed by the client team
 - client team provides IAM access to us and we deploy prometheus
   ourselves, within their VPC
 - we build in our own infrastructure and use VPC peering to access
   client team's private networks
 - we build in our own infrastructure and use VPC Endpoint Services as
   a way to get prometheus to access client team's private networks

[ec2 service discovery]: https://prometheus.io/docs/prometheus/latest/configuration/configuration/#%3Cec2_sd_config%3E

##### Provide an artefact to be deployed by the client team

The artefact we provide could take several forms:

 - an AMI (amazon machine image) which the client team deploys as an
   EC2 instance
 - a terraform module which the client team includes in their own
   terraform project

This model has the downside that it does not allow us to maintain
prometheus at a single common version, because we are at the mercy of
client teams' deploy cadences to ensure things get upgraded.

##### Client team provides IAM access so that we can deploy prometheus ourselves

In this model, the client team would create an IAM Role and allow us
to assume it, so that we can build our own prometheus infrastructure
within their VPC.

This would mean that the client team needs to do some work to provide
us with the correct IAM policies so that we can do what we need to do,
without giving us more capability than they feel comfortable with.

The client team would have visibility over what we had built, and
would be able to see it in their own AWS console.  However, they would
likely not have ssh access to the instance itself.

One possible issue with this model is that we're beholden on the
client to provide us with a CIDR range to deploy onto, and depending
on their existing estate, private IPs may be in short supply.

You're also dependent on their networking arrangements.  You will need
to ask questions like:

 - how can you get in and out of the network?
 - do you need to download packages to install?  how will that work?
 - do you need to download security updates?  how will that work?

The answers to these questions may be different for different client
teams.  This means that our prometheus pattern needs to be flexible
enough to cope with these differences, which will take extra
engineering effort.

We would need to work out what the integration point between our teams
would be.  This could be:

 - terraform outputs that appear in a remote state file
   - or stereotyped resource names/tags which can be matched using
     data sources

Whether or not we go with this option for deploying prometheus, if we
want to do ec2 service discovery (described above), prometheus will
need some sort of IAM access into the client team account anyway.

##### Use VPC Peering to provide access for prometheus to scrape target infrastructure

[VPC Peering][] is a feature which allows you to establish a
networking connection between two VPCs.  In particular, this is a
peering arrangement which means that the two networks on either side
of the VPC Peering arrangement cannot share the same IP address
ranges.

Crucially for us, the VPCs can be in different AWS accounts.

This means that we could build Prometheus in an account we own, and it
could access client team infrastructure over the peering connection.
Running our own infrastructure in our own account without being
dependent on client teams providing anything to us would make for
smoother operations and deployments for us.

This has a drawback for the client in that it adds extra points of
ingress and egress for traffic in the client networks.  This increases
the attack surface of the network which makes doing a security audit
harder and makes it harder to have confidence in the security of the
system.

There's also a drawback in terms of the combination of connections: as
RE builds more services that might be provided to client teams, and as
we extend these services to more client teams, we end up with N*M VPC
peering connections to maintain.

As RE (and techops more broadly) provides more services, client teams
end up having to consider more VPC Peering connections in their
security analyses, and this does not feel like a particularly scalable
way for us to offer services to client teams.

Finally, we believe that VPC Peering is something that has to be
accepted manually on the receiving account console (at least when
peering across AWS accounts), which compounds the scaling problem.

There are two sub cases worth exploring here:

###### A single prometheus VPC peers with multiple client VPCs

In this model, we would build a single prometheus service in a VPC
owned by us, and it would have VPC peering arrangements with multiple
client team VPCs in order to scrape metrics from each of them.

This has the benefit that we can run fewer instances to scrape metrics
from the same number of environments.

This has some drawbacks:

 - the single VPC becomes more privileged, because it has access to
   more environments.  This means that a compromise of the prometheus
   VPC could lead to a compromise of more clients' VPCs.

###### A single prometheus VPC peers with only a single client VPC

In this scenario, we would build a single prometheus in its own VPC
for each client team VPC we offer the service to.

This avoids some of the drawbacks of the previous case, in that the
prometheus does not have privileges to access multiple separate VPCs.

[VPC Peering]: https://docs.aws.amazon.com/AmazonVPC/latest/PeeringGuide/Welcome.html

##### Use VPC Endpoint Services to access scrape targets

This is a similar idea to VPC Peering.  [VPC Endpoint Services][] (aka
AWS PrivateLink) provides a way to provide services to a VPC, again
potentially in another account.

This allows you to make a single Network Load Balancer (NLB) appear
directly available (ie without going through a NAT Gateway) in another
VPC.

In the case of Prometheus, because it has a pull model, it seems
likely that the only way we could make this work would be by having
the client team provide the endpoint service and prometheus consume
it.  This would mean the client team would need to add a routing layer
(possibly path- or vhost-based routing, possibly using an ALB) to
distribute scrape requests to individual scrape targets.

This has the following advantages:

 - the IP address spaces in the prometheus VPC and the client team VPC
   are completely independent

However it has some drawbacks:

 - it feels like we're not using Endpoint Services in a designed use
   case. in other words, it feels like a bit of a hack.
 - Prometheus is designed to be close to its targets, so that there
   are fewer things to go wrong and prevent scraping.  The more layers
   of routing between prometheus and its scrape targets, the more
   chance we'll lose metrics during an outage, exactly when we need
   them.

[VPC Endpoint Services]: https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/endpoint-service.html

#### Decision

- Deploy Prometheus into Verify Performance Environment to test concept
- Deploy an instance of Prometheus into the client teams VPC
- Use Ubuntu 18.04 as the distribution for the instance [ADR-7](0007-use-ubuntu-18-04.md)
- Use Cloud Init to configure Prometheus [ADR-9](0009-use-cloud-init-to-build-prometheus-server.md)
- Use Verify infrastructure initially for the PoC [ADR-8](0008-use-of-egress-proxies.md)[ADR-10](0010-packaging-node-exporter-as-deb-for-verify.md)

#### Consequences

We have not yet solved the problem of how client teams provide alert
configuration to prometheus.

For the moment, it is acceptable to define configuration in
cloud-init, but this does not meet our need to update configuration
without rebuilding instances so we will need to revise this in future.
