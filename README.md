# 🔐 Okta → AWS IAM Identity Center SCIM Identity Lifecycle Lab

## Overview

This project demonstrates federated authentication and automated identity lifecycle management between **Okta (Identity Provider)** and **AWS IAM Identity Center (Service Provider)** using:

- **SAML 2.0** for authentication  
- **SCIM 2.0** for automated provisioning  
- **Group-based RBAC**  
- **Permission set mapping**  
- **Automated deprovisioning**

This lab simulates a real-world enterprise implementation of centralized identity governance across cloud platforms.

---

## Architecture

```mermaid
flowchart LR
  User[End User] --> Portal[AWS Access Portal]
  Portal -->|SP-initiated SAML| Okta[Okta IdP]
  Okta -->|SAML Assertion| IC[AWS IAM Identity Center]

  OktaUsers[Okta Users] -->|SCIM| IC
  OktaGroups[Okta Groups] -->|SCIM| IC

  IC --> PS[Permission Sets]
  PS --> Roles[AWS Account Roles]
```

---

## 1️⃣ Authentication (SAML)

- External IdP configured in IAM Identity Center  
- Okta metadata uploaded to AWS  
- NameID format set to `EmailAddress`  
- SP-initiated login used for stability  

---

## 2️⃣ Provisioning (SCIM)

Configured in:

- AWS → IAM Identity Center → Settings → Automatic Provisioning  
- Okta → AWS IAM Identity Center App → Provisioning  

Enabled:

- Create Users  
- Update User Attributes  
- Deactivate Users  
- Push Groups  

Result:

- Users automatically created in AWS  
- Groups automatically created in AWS  
- Membership synchronized  
- Deactivation enforced automatically  

---

## 3️⃣ Authorization (RBAC)

### Okta Groups

- `aws-ic-admins`  
- `aws-ic-dev`  
- `aws-ic-readonly`  

### AWS Permission Sets

| Permission Set | AWS Managed Policy |
|---|---|
| PS-Admin | AdministratorAccess |
| PS-Developer | PowerUserAccess |
| PS-ReadOnly | ReadOnlyAccess |

### Group → Permission Set Mapping

| Okta Group | AWS Permission Set |
|---|---|
| aws-ic-admins | PS-Admin |
| aws-ic-dev | PS-Developer |
| aws-ic-readonly | PS-ReadOnly |

Each group is assigned exactly one permission set per account.

---

## 🧪 Validation

### ✅ User Provisioning

Steps performed:

- Created test user in Okta  
- Assigned AWS IAM Identity Center application  
- Added user to `aws-ic-dev` group  

Observed:

- User automatically created in AWS  
- “Created by: SCIM” displayed in AWS  
- Group membership synchronized  
- Developer role visible in AWS Access Portal  

---

### ✅ Deprovisioning

Steps performed:

- User deactivated in Okta  

Observed:

- User disabled in AWS IAM Identity Center  
- AWS account access revoked automatically  

---

## ⚠ Troubleshooting & Lessons Learned

### SAML Rate Limiting (429 Errors)

Cause:
- Repeated direct hits to `/sso/saml` endpoint  
- Rapid retry loops during testing  

Resolution:
- Use SP-initiated login via AWS access portal  
- Avoid direct navigation to Okta SAML endpoint  
- Allow rate limit window to clear  

---

### 400 Bad SAML Request

Cause:
- Direct navigation to `/sso/saml` without proper `RelayState`  

Resolution:
- Always initiate login from AWS Access Portal URL  

---

### Infinite Loading / Role Not Displayed

Cause:
- Multiple permission sets assigned to a single group  

Resolution:
- Enforce 1 group → 1 permission set mapping  

---

## 🧠 Identity Engineering Concepts Demonstrated

- Separation of authentication and provisioning planes  
- SCIM as identity lifecycle data plane  
- SAML as federated authentication mechanism  
- Centralized RBAC using group mapping  
- Just-in-time AWS role assumption  
- Automated offboarding enforcement  
- Principle of least privilege  

---

## 🔒 Security Considerations

- No manually created IAM users in AWS  
- Centralized identity source (Okta)  
- Immediate access revocation upon deactivation  
- Reduced credential sprawl  
- Role-based access control enforcement  

---

## 🚀 Outcome

Successfully implemented:

- Federated SSO  
- Automated user provisioning  
- Automated group synchronization  
- Automated role assignment  
- Automated deprovisioning  

This architecture mirrors enterprise identity federation patterns used in production cloud environments.
