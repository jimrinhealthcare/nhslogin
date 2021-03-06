---
layout: base.njk
title: 4 Tokens
---

An ID Token is a security token that contains Claims about the Authentication of an End-User by the Platform, when using a Client and potentially other requested Claims. The Access Token is a credential that can be used by an application to access an API – initially the UserInfo endpoint.

The NHS login Platform uses signed JSON Web Tokens (JWTs) for ID Tokens and Access Tokens.

--- 

## 4.1 JWT Header

References:
- [RFC7519 – JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519)
- [RFC7515 – JSON Web Signature (JWS)](https://tools.ietf.org/html/rfc7515)

The JWT header will contain the following claims:

<dl><dt>Table 15 - JWT Header</dt></dl>

| Claim | Req? | Name | Description |
| ----- | ---- | ---- | ----------- |
| `alg` | mand | Algorithm used for <br>signing the JWT | `RS512` – `RSASSA-PKCS1-v1_5` <br>with the `SHA-512` hash algorithm |
| `typ` | mand | Type | "JWT" |
| `jku` | n/a  | JWK Set URL | Not supported, keys are <br>communicated outside the JWT, <br>see [RFC7519 – JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519), s2 |
| `jwk` | n/a  | JSON Web Key | Not supported, keys are <br>communicated outside the JWT, <br>see [RFC7519 – JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519), s2 |
| `x5u` | n/a  | X.509 URL | Not supported, keys are <br>communicated outside the JWT, <br>see [RFC7519 – JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519), s2 |
| `x5c` | n/a  | X.509 Certificate Chain | Not supported, keys are <br>communicated outside the JWT, <br>see [RFC7519 – JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519), s2 |

The JWT header **may** contain other Claims. Any Claims used that are not understood **must** be ignored by the RP.

---

## 4.2 ID Token Payload

References: 
- [OpenID Connect Core Specification](https://openid.net/specs/openid-connect-core-1_0.html)

The following Claims are used within the ID Token for all Oauth 2.0 flows used by OpenID Connect:

<dl><dt>Table 16.1 - id_token claims, standard</dt></dl>

| Claim | Req? | Name | Description |
| ---------------- | ---- | ---- | ----------- |
| `iss` | mand | Issuer Identifier for the Issuer of the response | The `iss` value is a case sensitive URL using the https scheme that contains scheme, host, and optionally, port number and path components and no query or fragment components. |
| `sub` | mand | Subject Identifier | A locally unique and never reassigned identifier within the Issuer for the End-User, which is intended to be consumed by the Client, e.g., `24400320` or `AitOawmwtWwcT0k51BayewNvutrJUqsvl6qs7A4`. <br>It **shall not** exceed 255 ASCII characters in length. <br> The `sub` value is a case sensitive string. |
| `aud` | mand | Audience(s) that this ID Token is intended for | It **shall** contain the Oauth 2.0 `client_id` of the Partner Services as an audience value. It **may** also contain identifiers for other audiences. <br>In the general case, the `aud` value is an array of case sensitive strings. <br>In the common special case when there is one audience, the `aud` value **shall** be a single case sensitive string. | 
| `exp` | mand | Expiration time on or after which the ID Token **must not** be accepted for processing | The processing of this parameter requires that the current date/time **must** be before the expiration date/time listed in the value. <br>Implementers **may** provide for some small leeway, usually no more than a few minutes, to account for clock skew. <br>Its value is a JSON number representing the number of seconds from 1970-01-01T0:0:0Z as measured in UTC until the date/time. <br>See [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) for details regarding date/times in general and UTC in particular. | 
| `iat` | mand | Time at which the JWT was issued | Its value is a JSON number representing the number of seconds from 1970-01-01T0:0:0Z as measured in UTC until the date/time. <br>See [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) for details regarding date/times in general and UTC in particular. |
| `jti` | mand | JWT unique identifier | Value is unique to each token created by the issuer. | 
| `auth_time` | cond | Time when the End-User authentication occurred | Its value is a JSON number representing the number of seconds from 1970-01-01T0:0:0Z as measured in UTC until the date/time. <br>When a `max_age` request is made or when `auth_time` is requested as an Essential Claim, then this Claim is **required**; otherwise, its inclusion is **OPTIONAL**. <br>(The `auth_time` Claim semantically corresponds to the [OpenID 2.0 PAPE](http://openid.net/specs/openid-provider-authentication-policy-extension-1_0.html) `auth_time` response parameter). |
| `nonce` | cond | String value used to associate a Client session with an ID Token, and to mitigate replay attacks | The value is passed through unmodified from the Authentication Request to the ID Token. <br>If present in the ID Token, Clients **must** verify that the nonce Claim Value is equal to the value of the nonce parameter sent in the Authentication Request. <br>If present in the Authentication Request, the Platform **must** include a nonce Claim in the ID Token with the Claim Value being the nonce value sent in the Authentication Request. <br>The Platform will perform no other processing on nonce values used. <br>The nonce value is a case sensitive string. |
| `acr` | n/a  | Authentication Context Class Reference | Not supported - superseded by `vot`. |
| `amr` | n/a  | Authentication Methods References | Not currently supported. |
| `azp` | n/a  | Authorized party – the party to which the ID Token was issued | Not currently supported. |
| `vot` | mand | Vectors of Trust | The level to which the user’s identity has been verified. <br>See [5.1 Vectors of Trust](https://nhsconnect.github.io/nhslogin/interface-spec-doc-5) for values. |
| `vtm` | mand | Vector Trust Mark | https URI of the vtm claim <br>See [5.1.5 Trustmark](https://nhsconnect.github.io/nhslogin/interface-spec-doc-5).|
| `family_name` | cond | Family Name | Surname(s) or last name(s) of the End-User |
| `birthdate` | cond | Birthdate | If the user’s date of birth is available, it is returned in ISO8601‑2004 format, YYYY-MM-DD. |

<dl><dt>Table 16.2 - id_token claims, NHS login extensions</dt></dl>

| Claim | Req? | Name                    | Description |
| ---------------------------- | ---- | ----------------------- | ----------- |
| `nhs_number`                 | cond | NHS Number              | If the user’s NHS Number is known, then it **shall** be included (subject to the profile scope being requested). <br>Will be represented as a 10-digit string. |
| `identity_proofing_level`    | cond | Identity Proofing Level | User’s identity proofing level **shall** be included (subject to the profile scope being requested). <br>It will contain one of the values defined in [5.1.1 Verification of Identity Levels](https://nhsconnect.github.io/nhslogin/interface-spec-doc-5) and Table 18 – e.g. “P0”, “P9”. |

ID Tokens **may** contain other Claims. Any Claims used that are not understood **must** be ignored by the RP. 

The following is a non-normative example of the set of Claims (the JWT Claims Set) in an ID Token:

``` html
  {
   “iss”: “ https://auth.login.nhs.uk”,
   “sub”: “24400320”,
   “aud”: “s6BhdRkqt3”,
   “nonce”: “n-0S6_WzA2Mj”,
   “exp”: 1311281970,
   “iat”: 1311280970,
   “auth_time”: 1311280969,
   “vot”: “P9.Ca.Cc”,	
   “vtm”: “https://auth.login.nhs.uk/trustmark/auth.login.nhs.uk”
   “nhs_number”: “4447685222”,
   “birthdate”: “2001-12-30”,
   “family_name”: “Johnson”,
   “identity_proofing_level”: “P9”
  }
```

---

## 4.3 Access Token Payload

References:
- [OpenID Connect Core Specification](https://openid.net/specs/openid-connect-core-1_0.html)
- [Access Tokens and Audit (JWT)](https://developer.nhs.uk/apis/spine-core/security_jwt.html)

<dl><dt>Table 17.1 - access_token claims, standard</dt></dl>

| Claim | Req? | Name | Description |
| ----- | ---- | ---- | ----------- |
| `iss` | mand | Issuer Identifier for the Issuer of the response | The iss value is a case sensitive URL using the https scheme that contains scheme, host, and optionally, port number and path components and no query or fragment components. |
| `sub` | mand | Subject Identifier | A locally unique and never reassigned identifier within the Issuer for the End-User, which is intended to be consumed by the Client, e.g., `24400320` or `AitOawmwtWwcT0k51BayewNvutrJUqsvl6qs7A4`. <br>It **shall not** exceed 255 ASCII characters in length. <br>The `sub` value is a case sensitive string. |
| `aud` | mand | Audience(s) that this ID Token is intended for | It **shall** contain the Oauth 2.0 `client_id` of the Partner Service as an audience value. It **may** also contain identifiers for other audiences. <br>In the general case, the `aud` value is an array of case sensitive strings. <br>In the common special case when there is one audience, the `aud` value **shall** be a single case sensitive string. |
| `exp` | mand | Expiration time on or after which the ID Token **must not** be accepted for processing | The processing of this parameter requires that the current date/time **must** be before the expiration date/time listed in the value. <br>Implementers **may** provide for some small leeway, usually no more than a few minutes, to account for clock skew. <br>Its value is a JSON number representing the number of seconds from 1970-01-01T0:0:0Z as measured in UTC until the date/time. <br>See [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) for details regarding date/times in general and UTC in particular. |
| `iat` | mand | Time at which the JWT was issued | Its value is a JSON number representing the number of seconds from 1970-01-01T0:0:0Z as measured in UTC until the date/time. <br>See [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) for details regarding date/times in general and UTC in particular. |
| `jti` | mand | JWT unique identifier | Value is unique to each token created by the issue. |
| `scope` | mand | Scopes that the `access_token` provides access to | A space-separated list of scopes for which the token is issued. <br>This list will be scopes requested in the authentication request (see [3.4.1 Authentication request](https://nhsconnect.github.io/nhslogin/interface-spec-doc-3/)) or a subset of them. |
| `auth_time` | cond | Time when the End-User authentication occurred | Its value is a JSON number representing the number of seconds from 1970-01-01T0:0:0Z as measured in UTC until the date/time. <br>When a max_age request is made or when `auth_time` is requested as an Essential Claim, then this Claim is **required**; otherwise, its inclusion is **OPTIONAL**. (The `auth_time` Claim semantically corresponds to the [OpenID 2.0 PAPE](http://openid.net/specs/openid-provider-authentication-policy-extension-1_0.html) `auth_time` response parameter.) |
| `acr` | n/a | Authentication Context Class Reference | Not supported – superseded by `vot`. |
| `vot` | mand | Vectors of Trust | The level to which the user’s identity has been verified. <br>See [5.1 Vectors of Trust](https://nhsconnect.github.io/nhslogin/interface-spec-doc-5/) for values. |
| `vtm` | mand | Vector Trust Mark | https URI of the `vtm` claim <br>See [5.1.5 Trustmark](https://nhsconnect.github.io/nhslogin/interface-spec-doc-5/)  |

<dl><dt>Table 17.2 - access_token claims, NHS login extensions</dt></dl>

| Claim        | Req? | Name       | Description |
| ------------ | ---- | ---------- | ----------- |
| `nhs_number` | cond | NHS Number | If the user’s NHS Number is known, then it **shall** be included (subject to the profile scope being requested). <br>Will be represented as a 10-digit string. |

<dl><dt>Table 17.3 - access_token claims, Spine Core extensions</dt></dl>

| Claim        | Req? | Name       | Description |
| ------------ | ---- | ---------- | ----------- |
| `reason_for_request` | opt | The identified the purpose for which the request is being made. | Purpose for which access is being requested. Will contain `patientaccess`. <br>See [Access Tokens and Audit (JWT)](https://developer.nhs.uk/apis/spine-core/security_jwt.html). | 
| `requesting_system` | opt | Identifier for the system or device making the request | This is an identifier for the deployed client system that has been authorised to make API calls. <br>In the case of Spine-enabled clients (or those using the SSP to broker API calls), this will be a Spine Accredited System ID (ASID). <br>The naming system prefix for the ASID will be [https://fhir.nhs.uk/Id/accredited-system](https://fhir.nhs.uk/Id/accredited-system). <br>See [Access Tokens and Audit (JWT)](https://developer.nhs.uk/apis/spine-core/security_jwt.html). | 
| `requesting_patient` | opt | NHS Number | If this authorisation relates to a citizen, this attribute will hold the NHS Number of the citizen. <br>The naming system prefix for the NHS Number will be [http://fhir.nhs.net/Id/nhs-number](http://fhir.nhs.net/Id/nhs-number). <br>See [Access Tokens and Audit (JWT)](https://developer.nhs.uk/apis/spine-core/security_jwt.html). |

Access Tokens **may** contain other Claims. Any Claims used that are not understood **must** be ignored by the RP.

---

## 4.4 JOSE Signing

References: 
- [RFC7519 – JSON Web Token (JWT)](https://tools.ietf.org/html/rfc7519)
- [RFC7515 – JSON Web Signature (JWS)](https://tools.ietf.org/html/rfc7515)

The JWT will be signed using the RSASSA-PKCS1-v1_5 with the SHA-512 hash algorithm (“RS512”)
