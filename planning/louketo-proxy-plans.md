# Louketo proxy plans

* **Status**: Draft #1
* **Author**: [stianst](https://github.com/stianst)
* **JIRA**: [KEYCLOAK-8398](https://issues.redhat.com/browse/KEYCLOAK-8398)

# Use-cases

## Server-side Web Application

Secure new or existing server-side web applications with an external OpenID Connect provider through a proxy.

The application may or may not have built-in authorization. If the application does not have built-in authorization it should be possible to enable authorization at the proxy level, including support for an external PDP.

## REST API

Secure new or existing REST APIs with bearer tokens.

The service may or may not have built-in authorization. If the service does not have built-in authorization it should be possible to enable authorization at the proxy level, including support for an external PDP.

## Single-page Application

Secure new or existing single-page (HTML5) application that invokes REST APIs on the same domain.

## Secure outbound requests

Ability to act as an outgoing web proxy in order to add bearer tokens to outgoing request. It should be possible to obtain the bearer token from the incoming request, from a service account (client credentials grant) or from a Security Token Service.

## Kubernetes Operator

Automatically secure applications and services deployed to Kubernetes through a Kubernetes Operator.

# Requirements

## General

* Support any certified OpenID Connect provider

* Features should be limited to address use-cases layed out in this document. It should not be a general purpose proxy solution (load balancing, rate limiting, route matching, etc.)

* Distributed as binaries and container images

* Deployed as a side-car, meaning the proxy should be deployed alongside each instance of the application or service

* Easy to use. It should be easy to configure, deploy and manage the proxy, which means usability including documentation should be a high priority

* SSL support

    * Enable SSL with user supplied certificate

    * Enable SSL using Let’s Encrypt

    * Enable SSL using ACME Protocol

* Secure Headers

    * Set HTTP response headers by default, with ability to customise when required

* Client Authentication with the IdP via secret, JWT and mTLS

* Follow best practices

    * OAuth, OpenID Connect, FAPI read/write profile

    * Security Headers

    * OWASP

## Web Application

* Authenticate users through an external OpenID Connect provider using Authorization Code Flow

    * Ability to configure parameters such as scope, max_age, ui_locales and acr_values

        * Ability to define scopes based on request path

    * Ability to require specific claims, including custom claims from the ID token

    * Redirect to requested path after authentication

* Stateless sessions via cookies

    * Optional encryption of tokens stored within cookies, including support for rotation of encryption keys

* Stateful sessions via external store such as Infinispan or Reddis

* Single Logout

    * Initiate logout via RP initiated logout, including passing id_token_hint

        * Redirect to configurable path after logout

    * Support OpenID Connect Front-Channel logout (stateless sessions)

    * Support OpenID Connect Back-Channel logout (stateful sessions)

* Forward information to web application via headers

    * ID Token

    * Access Token

    * Token Introspection response

    * Standard claims and custom claims from tokens

* Automatically refresh tokens as needed

* Standard pages

    * Login - configurable page showing a link to login

    * Logout - configurable page showing the user has been logged-out

* Standard endpoints

    * Login - initiate login

    * Login page - display a login page

    * Logout - initiate logout

    * Callback - callback endpoints for login and logout

    * Health - health endpoint, including option to configure a health endpoint for the application

    * Metrics - Prometheus metrics endpoint providing HTTP request statistics

* CSRF protection

## Service

* Secure incoming request with bearer token, see token processing for details

* Secure incoming request with session when service is deployed on the same domain as the application

* Support external PDP

* Support setting CORS headers

## Token processing

* Key management

    * Obtain public keys from IdP (jwks_url)

        * At startup

        * Refresh keys automatically on demand (unknown kid)

    * Supply signing keys directly in config

* Support for non-opaque tokens (JWS and JWE)

    * Should not require a request to IdP per incoming request

    * Validate token using keys from IdP

    * Support standard signing and encryption algorithms as specified in JWK specification

* Support for opaque tokens

    * Validate token using token introspection endpoint provided by IdP

    * Support caching token introspection responses to reduce requests to IdP

        * Locally

        * External cache such as Infinispan or Reddis

* Support validating/requiring standard and custom claims based on request path

## Outgoing Web Proxy

* Provide an outgoing web proxy to add security context (bearer token) to outgoing request

* Add bearer token to outgoing request, where bearer token is obtained from one of the following

    * Corresponding incoming request

    * Service account (client credential grant)

    * Secure Token Service

## Kubernetes Operator

* Automatically secure applications through a Kubernetes Operator

    * Application defines a custom CR, or annotates the deployment

    * Operator injects the proxy as a sidecar and updates service ports

* Automatically register clients in IdP through a custom CR or using dynamic client registration

## Plugins/extensibility

* Plugin custom store for sessions and tokens

* Define custom security headers

* Define custom headers sent to upstream

* Custom claim processing

* Custom request/response handlers

# Relevant specifications

* OpenID Connect Core

* OpenID Connect Discovery

* OpenID Connect Session Management (limited to RP initiated logout)

* OpenID Connect Front-Channel Logout

* OpenID Connect Back-Channel Logout

* OpenID Connect Dynamic Registration

* JSON Web Signature (JWS)

* JSON Web Encryption (JWE)

* JSON Web Key (JWK)

* OAuth 2.0 Token Introspection

* OAuth 2.0 Security Best Current Practice

* Financial-grade API - Part 1: Read-Only API Security Profile

* Financial-grade API - Part 2: Read and Write API Security Profile

* User Managed Access 2.0

* OAuth 2.0 Mutual TLS Client Authentication and Certificate Bound Access Tokens
