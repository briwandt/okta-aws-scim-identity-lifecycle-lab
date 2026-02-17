# Okta → AWS IAM Identity Center SCIM Identity Lifecycle Lab

## Overview

This project demonstrates federated authentication and automated identity lifecycle management between Okta and AWS IAM Identity Center using SAML 2.0 and SCIM 2.0.

The implementation enforces centralized identity governance, role-based access control (RBAC), and automated provisioning/deprovisioning across cloud environments.

---

## Architecture

```mermaid
flowchart LR
    User --> AWS[AWS Access Portal]
    AWS --> Okta[SAML Authentication]
    Okta --> Identity[AWS IAM Identity Center]

    OktaUsers -->|SCIM| Identity
    OktaGroups -->|SCIM| Identity

    Identity --> PermissionSets
    PermissionSets --> AWSAccount
Implementation Summary
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

Group	Permission Set
aws-ic-admins	PS-Admin
aws-ic-dev	PS-Developer
aws-ic-readonly	PS-ReadOnly

Permission sets mapped to AWS account roles.

Validation

User created in Okta → automatically provisioned in AWS (Created by: SCIM)

Group membership synced to AWS

Role displayed in AWS access portal

User deactivation in Okta revoked AWS access automatically
Lessons Learned

Always use SP-initiated SAML login to avoid rate limiting.

Avoid direct navigation to /sso/saml endpoint.

Enforce one permission set per group to prevent role ambiguity.

SCIM and SAML operate as separate identity planes.
