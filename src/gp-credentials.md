---
layout: base.njk
title: Using NHS login to create or retrieve GP credentials
---

## Introduction

If you are an IM1 supplier, learn how to use NHS login to create or retrieve GP credentials. This means users do not have to go their GP practice to register for your service.

NHS login supports a scope of `gp_integration_credentials` which will allow a Relying Party (RP) to retrieve the following user claims:

- Account ID
- ODS Code
- Linkage Key

These three pieces of information are required by IM1 paired services to create an IM1 token which is then used by the RP to invoke the GP system patient facing APIs. 

The RP (service) must be IM1 enabled for enabling this scope in production environment. 

Retrieving these items of information via NHS login removes the need for the user to enter them manually, as well as being able to seamlessly register previously unregistered GP Online users – preventing a visit to a GP Practice.

![Diagram](nhslogin/images/gp-credentials-diagram.png)

This diagram illustrates
1. How NHS login processes a request from an RP for `gp_integration_credentials` via the **/userinfo** endpoint
2. How NHS login validates `gp_integration_credentials` on each request and how `gp_integration_credentials` are refreshed if the information held by NHS login is invalid

---

## GP integration credentials validation

When `gp_integration_credentials` scope is requested by an RP, NHS login performs a background check of the validity of a stored IM1 token. As this check runs in the background asynchronously, it is the responsibility of the RP to also validate their IM1 token (if one exists).

If the RP determines that the claims returned from `gp_integration_credentials` scope are not valid it can request refreshed claims from NHS login by re-starting the login process.

Optional - the RP can utilise the `asserted_login_identity` request parameter to avoid the user needing to re-authenticate.

---

## GP integration credentials are no longer valid

The GP credentials might no longer be valid due to:

1. The user has moved GP Practice
2. The GP Practice has changed GP systems
3. Practices merge / divides
4. The user’s GP online access been revoked at the GP system e.g. for multiple failed login attempts

In cases 1, 2 and 3 generally the ODS code for the user changes and will be detected by NHS login automatically via a separate background process that validates PDS information relating to the user.

### Assumptions

- When GP credentials (linkage keys) expire or get invalidated all the IM1 tokens generated using them also expire/get invalidated. So in case the RP has any process to expire or invalidate the GP credentials (linkage keys) then it should be disclosed/discussed with NHS login.
- RP should cater for user journey which prevents the user getting into an endless “retrieve GP credential” loop if the credentials genuinely can’t be retrieved.

---

## GP systems coverage

Currently NHS login provides coverage for GP login credentials

- EMIS: 100%
- TPP: 100%
- Vision: 100% (250 in total)
- Microtest*: None (36 in total)

**GP login credentials (linkage keys) for Microtest returned by NHS login are dummy in nature, to maintain the uniform behaviour of the service. These GP login credentials cannot be used by RPs for connecting to Microtest.*