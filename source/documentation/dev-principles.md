## Development principles

These are some principles that may be useful to keep in mind when developing our services.

### Definition of done

Having a shared understanding of when something is "done" within a team can have two benefits:

* estimates of effort for completing user stories may be more accurate and uniform across members of the team

* it can be used as a checklist to make sure every step of the work is completed

In general, we tend to under-estimate effort as we often focus our attention mostly on the implementation step. Thus, it is helpful to think about all the many other steps around it.

Here we assume user research and high-level planning have been already conducted and there is a well-defined user story ready to be "played" by an engineer or designer.

The definition of done is likely to vary depending on the phase the project is at (e.g. alpha vs beta) and on the peculiarities of the project itself.

However, the following can be a good starting point:

* the person/pair working on the story (owner) has formulated a plan on how to implement the story
* the story has been kicked-off with the team (if the story is a complex one)
* the story has clear and verifiable acceptance criteria, validated at the kickoff
* all concerns or questions raised during kick-off have been addressed
* the owner of the story work on the implementation
* the implementation has been tested
* any issue following the testing has been addressed
* any relevant documentation has been created
* the story owner(s) raises a PR(s)
* another member of the team review that PR(s)
* any comment from that review gets addressed
* the owner merges the PR to master
* all the automated tests (e.g. accessibility, integration, performance, ...) pass
* the work has been validated against the acceptance criteria by another team member
* the change has been released to all relevant environments and everything looks fine
* Show and Tell (optional)
* Done!
