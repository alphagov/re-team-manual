# Reliability Engineering on-call escalation

This page is intended to provide information to people on the RE on-call
escalations rota.

## What is the RE on-call escalation rota?

Each of the teams in Reliability Engineering operate an on-call rota to provide
out-of-hours cover for the various services RE supports. This is a mix of
primary support for things RE is responsible for, such as the PaaS and Alert
Manager, and backup for on-call engineers in other service teams.

To support this we also maintain an on-call escalation rota of RE leadership
folks to serve the following purposes:

* To act as an automatic escalation if an on-call engineer fails to respond to
an incident. If this happens the escalation person is responsible for finding
someone from the team who can respond to the incident.
* To provide leadership-level backup for RE on-call engineers if an incident
requires leadership decision making or a broader response involving updating
comms or activating other engineers.

The on-call escalation is __not__ expected to actually fix issues or provide
technical expertise. Rather, the escalation is there to provide leadership-level
backup, decision making and co-ordination of serious incidents.

## Where can I find the RE escalations rota?

The RE escalations rota and the person currently on-call for escalations [can be
seen via the RE escalations rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#P02OIA8) or
via the [rotas
app](https://rotas.cloudapps.digital/calendars/pagerduty:a9242f17-f029-418d-8449-d6ea6d9aaf6e).

## Important contacts

### Cyber Security

Cyber Sec On-call Duty person: +44 1857 341 102

Cyber Sec primary on-call: [Cyber Security primary on-duty rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#P6ZV900)

Cyber Sec secondary on-call: [Cyber Security secondary on-duty rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#P2PT1KS)

Slack channels: #security or #cyber-security-help

### GOV.UK

GOV.UK incidents are managed using [the GOV.UK Incident
Process](https://docs.publishing.service.gov.uk/manual/incident-management-guidance.html).
The Incident Lead and Comms Leads will always be GOV.UK people, except for long
running infrastructure incidents where the Incident Lead might switch to a
member of the RE GOV.UK team, but comms will always be handled by GOV.UK so the
RE escalation person will never need to do this.

Slack: #govuk-2ndline and #govuk-incident

Primary on call: [GOV.UK Primary rota in PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules/P479TSJ)

Secondary on call: [GOV.UK Secondary rota in PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules/P752O37)

Programme leadership escalation: [GOV.UK Programme Escalations rota in PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules/PCK3XB2)

RE GOV.UK on call: [GOV.UK RE out of hours rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#P24F5Q2)

### GOV.UK PaaS

The PaaS team Incident Process is documented [in the PaaS team
manual](https://team-manual.cloud.service.gov.uk/incident_management/incident_process).

The PaaS team use statuspage.io to communicate with their users about incidents.
As the escalation person you may be required to act as comms lead during
out-of-hours incidents so will need access to the PaaS statuspage.io account.
Information on this is documented [in the PaaS team
manual](https://team-manual.cloud.service.gov.uk/team/statuspage).

Slack: #paas and #paas-incident

Out of hours on call: [PaaS team out of hours rota in PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#PUOJ449)

### Portfolio programme

The service teams in the Portfolio programme provide their own primary on-call
out-of-hours cover, but can activate the RE Portfolio team for out-of-hours Ops
support via the [RE Portfolio out-of-hours rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#P3OL0L1).

#### GOV.UK Notify

Notify use a 3rd party company called Unboxed to provide out-of-hours cover for
the Notify service.

Unboxed: Contact details can be found [in
PagerDuty](https://governmentdigitalservice.pagerduty.com/users/PRY1MIA)

Notify leadership escalations: [Notify Managers rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#PLMSKGI)

#### GOV.UK Pay

Primary on call: [Pay: Primary out of hours rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#PZ30QWZ)

Pay leadership escalations: [Pay: escalation rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#P8NNTF8)

### GOV.UK Verify

Verify provide their own primary and secondary on-call for their services.
However, the RE autom8 team provide ops support out-of-hours through [the autom8
schedule in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#P7EUK0J).

Slack: #verify-2ndline and #verify-incidents

Primary on call: [GOV.UK Verify Primary out of hours rota in
PagerDuty](https://governmentdigitalservice.pagerduty.com/schedules#PG8WL6J)

Programme leadership escalation: [Verify SMT contact in
PagerDuty](https://governmentdigitalservice.pagerduty.com/users/PAEQAD2)

### Third party providers

AWS Technical Account Manager (TAM): Chris Pates crpates@amazon.co.uk 

Service requests can be raised through [the AWS
console](https://console.aws.amazon.com). Any requests raised as 'critical' or
'urgent' will cause our Technical Account Manager to be paged.

#### UKCloud

UKCloud emergency number: 01252 303300

UKCloud emergency email: support@UKCloud.com

UKCloud Technical Account Manager: Brendan O'Connell boconnell@ukcloud.com +44 7825 709 673

#### Carrenza

Carrenza emergency number: 020 7858 403

#### Avien Elasticsearch

Email: support@aiven.io

