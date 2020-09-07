### 11. SLI for how reliably do we deliver pages

Date: 2018-10-25

#### Status

Accepted

#### Context

https://trello.com/c/56qyWJ60/675-show-an-sli-how-reliably-do-we-deliver-pages

We wish to have a service level indicator for how reliably do we deliver pages. We believe the way to measure this is to calculate for a given time period:

`the number of incidents created in pagerduty / the number of incidents that we expect to have been created in pagerduty`

##### Calculating the number of incidents created in pagerduty

We think we can work out how many incidents there have been within a provided timeframe using the pagerduty API. We have done this for our pagerduty account successfully using a few lines of Ruby code. We would need to have access to every other teams account in order to know about all incidents and not just ones for our team. We did not spend time trying to actually do this. We would also need to run a exporter to export this information from pagerduty so Prometheus could scrape it.

##### Calculating the number of incidents that we expect to have been created in pagerduty

The main source of information is the `ALERTS` metric in Prometheus but there are a few problems with this.

###### Problem 1

At the moment we don’t have a way of identifying which alerts are tickets and which are pages. Some metrics include this information using labels but not all do. We could solve this if needed to by adding severity labels to all alerts and adding documentation so our users would also do this.

###### Problem 2

Prometheus doesn’t provide a metric for how many incidents we believe should have been created. Prometheus instead has metrics which measure if alerts are currently firing. We would need to reliabily turn the `ALERTS` metrics into a single metric for how many incidents we believe should have been created.

We came up with:

```count(ALERTS{alertstate="firing", severity="page"}) or vector(0)```

We think that from here we can use the `increase` function to tell us how many times alerts have begun firing. To use this we think we would need to use recording rules as per https://www.robustperception.io/composing-range-vector-functions-in-promql to turn our query into a single range vector.

At that point we should have a number for how many alerts have begun firing in a given time period. However we are not confident that this number is equal to the number of pagerduty incidents we expect to be created. The reason for this is because Alertmanager [groups firing alerts](https://prometheus.io/docs/alerting/alertmanager/#grouping), meaning multiple firing alerts may only result in one notification and therefore one incident. A potential way around this would be to try and edit the grouping behaviour of Alertmanager using it's config but it [does not look it's possible to turn it off completely](https://groups.google.com/forum/#!topic/prometheus-users/35znfrwu_z8). There could also be issues if an alert fires, then resolves itself, and then fires immediately after only triggering an single incident.

#### Decision

We have decided not to try and implement this SLI at the moment as we are not confident that we can accurately calculate the number of incidents we expect in Pagerduty for a given time period using metrics from Prometheus. It might be possible but would require a few days to investigate if so and would likely end up with a somewhat complex system to measure this SLI. We could change this decision if we become more confident.

#### Consequences

We do not measure one of our main user journeys accurately and thus cannot alert if there are problems with it. We may instead have to use a proxy or measure individual components that make up the user journey instead which may be easier to measure but less accurate.
