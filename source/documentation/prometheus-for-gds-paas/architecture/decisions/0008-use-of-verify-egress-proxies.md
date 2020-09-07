### 8. Use of Verify Egress Proxies

Date: 2018-08-15

#### Status

Accepted

#### Context

Verify employ egress proxies to control access to external resources.
These are a security measure to help prevent data from being exfiltrated from within Verify.
The Prometheus server will need access to external resources, notably an Ubuntu APT mirror during the bootstrap process.
The Prometheus server should not set up its own routes to bypass the egress proxy by using a NAT gateway or Elastic IP, as this will potentially open up a route for data exfiltration.

#### Decision

The Prometheus server should use Verify's egress proxies and choice of APT mirror.

#### Consequences

This creates a binding to Verify's infrastructure, this should be considered as a temporary compromise until the Prometheus server is built as a machine image
