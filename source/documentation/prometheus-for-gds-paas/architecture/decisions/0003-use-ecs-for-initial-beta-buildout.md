### 3. Use Amazon ECS for initial beta build-out

Date: 2018-06-21

(although note that this ADR was written post hoc, a couple of months
after the decision was made)

#### Status

Superseded.

We have now migrated prometheus off ECS and on to EC2.  The rest is
covered in ADR #12.

#### Context

Existing self-hosted infrastructure at GDS has been managed in code
using tools like puppet, but in a somewhat unplanned way with each team
doing things differently, little sharing of code, and much reinvention
of wheels.  We would like to learn about other ways of deploying
infrastructure which encourage consistency: in terms of code
artifacts, configuration methods, and such like.

Systems such as Kubernetes and Amazon ECS are coalescing around Docker
as a standard for packaging software and managing configuration.

#### Decision

We will build our initial prometheus beta in Amazon ECS, and assess
how effective it is.  We will review this decision once we have learnt
more about both prometheus and ECS.

#### Consequences

There may be ways in which Prometheus's opinions clash with ECS's
opinions.  For example, Prometheus by default uses local disk for
storing state, which may mean that we need to pin the prometheus
container to a single underlying instance so that it always gets the
same local disk.
