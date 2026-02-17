# Okta → AWS IAM Identity Center SCIM Identity Lifecycle Lab

## Overview
This project demonstrates federated authentication and automated identity lifecycle management between Okta and AWS IAM Identity Center using **SAML 2.0** and **SCIM 2.0**.

The implementation enforces centralized identity governance, role-based access control (RBAC), and automated provisioning/deprovisioning across cloud environments.

---

## Architecture

```mermaid
flowchart LR
    User --> AWS[AWS Access Portal]
    AWS --> Okta[SAML Authentication]
    Okta --> Identity[AWS IAM Identity Center]

    OktaUsers[Okta Users] -->|SCIM| Identity
    OktaGroups[Okta Groups] -->|SCIM| Identity

    Identity --> PermissionSets[Permission Sets]
    PermissionSets --> AWSAccount[AWS Account]
mplementation Summary
Authentication

SAML federation configured between Okta and AWS IAM Identity Center

SP-initiated login via AWS access portal

Email-based NameID mapping

Provisioning

SCIM endpoint enabled in AWS

API integration configured in Okta

Automatic user creation, update, and deactivation enabled

Group push enabled

Authorization

Okta Groups mapped to AWS Permission Sets:

Okta Group	AWS Permission Set
aws-ic-admins	PS-Admin
aws-ic-dev	PS-Developer
aws-ic-readonly	PS-ReadOnly

Permission sets are then assigned to AWS account(s) to grant access via IAM Identity Center.

Validation

User created in Okta → automatically provisioned in AWS (Created by: SCIM)

Group membership synced to AWS

Role displayed in AWS access portal

User deactivation in Okta revoked AWS access automatically

Lessons Learned

Prefer SP-initiated login (AWS access portal URL) when testing SAML flows.

Avoid direct navigation to Okta /sso/saml endpoints (can produce “Bad SAML request”).

Keep one permission set per group to avoid role ambiguity.

SCIM (provisioning) and SAML (authentication) are separate planes—both must be correct.
