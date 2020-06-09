## Runbook

### Creating new AWS accounts

Reliability Engineering are responsible for creating and setting up new AWS
accounts for the rest of GDS. These should be joined to the GDS organisation so
the account appears on the consolidated billing. The root credentials should
also be secured in a standard way.

Requests for new AWS accounts should be made via the [gds request an aws account
app](https://gds-request-an-aws-account.cloudapps.digital). When someone submits
a request the app generates new terraform config, which needs to be applied
using the re-infra-release-automation tooling.

The steps are:

#### Read the "New AWS account request" email.

The 'gds request an aws account app' sends an email to the
gds-aws-account-management@digital.cabinet-office.gov.uk address. There are
basic instructons in the email to get you started.

#### Review and merge the pull request

The app generates terraform config in JSON format and opens a pull request with
the changes against the [aws-billing-account git
repo](https://github.com/alphagov/aws-billing-account/). You need to review the
PR, and if it looks sensible, approve and merge it.

#### Run the terraform

The aws-billing-account terraform is applied via the [re-infra-release-automation
tooling](https://github.com/alphagov/re-infra-release-automation).

The `aws_accounts` release will attempt to assume an org-admin-access
role in the gds-billing account from your gds-users account. **You must have your
gds-users account setup with MFA enabled for this to work and also you must have
a trust relationship configured in the org-admin-access role**.

Your `~/.aws/config` file should contain the following:

```
[profile gds-users]

[profile gds-users-admin]
role_arn = arn:aws:iam::622626885786:role/grant-iam-admin-access
source_profile = gds-users
mfa_serial = arn:aws:iam::622626885786:mfa/your.name@digital.cabinet-office.gov.uk

[profile gds-billing-org-admin]
role_arn       = arn:aws:iam::988997429095:role/org-admin-access
source_profile = gds-users
mfa_serial     = arn:aws:iam::622626885786:mfa/your.name@digital.cabinet-office.gov.uk
```

Run the aws_accounts release to apply the terraform:

```
$ ./do_release aws_accounts
```

This will run a plan. Review the output and if you're happy, allow the release
to run an apply.

The terraform will have created the new account and joined it to the
organisation. However, it is not able to complete the steps required to secure
the root user. It will prompt you to complete a list of manual steps.

#### Trigger a password reset for the root user

In a browser open the [AWS console login page](http://console.aws.amazon.com/).
Use the root user email address used when the account was created. This will be
in the terraform output. Select the "forgot my password" link.

Generate a long, random password:

```
$ pwgen 40 -y
```

You will have received a password reset email to the aws-root-accounts@ email
address. Click the reset link in the email and use the password generated above
to reset the password and log into the account.

**You do not need to store the new password anywhere and should be forgotten**.
If we need to access the account using the root user we can go through the
password reset process using the MFA token, set up in the next step.

#### Set up MFA for the root user

The MFA for the root user on all new accounts are stored on a pair of Yubikeys,
stored in the safe in the safe filestore room.

In the AWS console, click on the account name in the top right, then select My
Security Credentials. Then select MFA, then Activate MFA, then Virtual MFA
device. This will display a QR code.

Click the "Show secret key for manual configuration", which will display the key
as a string which you can seed the yubikeys with.

Insert one of the two yubikeys into your laptop. You can view the existing
tokens with:

```
$ ykman oath list
Amazon Web Services:root-mfa@123456720024 (foo-bar-bob)
...
```

The yubikey can hold up to 32 MFA tokens.

Add the new MFA token:

```
$ ykman oath add "Amazon Web Services:root-mfa@[new account ID] (gds-whatever)" '[MFA token in string form]'
```

Generate two consecutive codes and copy into the AWS console to complete the
seeding. The yubikey will generate a new code every 30 seconds:

```
$ ykman oath code 'Amazon Web Services:root-mfa@[new account ID] (gds-whatever)'
```

#### Add the MFA token to the second yubikey

**Remember to add the MFA token to the second yubikey** by running the `ykman
oath add` command from above.

#### Test you can access the root user using the new credentials

In a private browsing window in your browser open the console and test you can
log into the new account as the root user using the password you generated
earlier and an MFA token from the yubikeys.

#### Edit the trust relationships on the bootstrap role

Back in the re-infra-release-automation window, confirm you've completed the
root user credentials steps. It will display a template of the policy that needs
to be copied onto the bootstrap role in the new account. In the console go to
the IAM console and find the bootstrap role, edit the trust relationships.
Replace the text with the text from the release-automation, replacing
the email address with the email addresses from the original request email.

#### Inform the requestor the account is ready to use.

Tell the requestor the account is ready to use and they can assume the
bootstrap role from their gds-users identity to gain admin access.

#### Put the yubikeys back in the safe!!!

The work is not complete until the yubikey is secured back in the safe.
