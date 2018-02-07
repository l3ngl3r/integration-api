# PDXC API Service Contract

## <a href="#purpose" id="purpose"></a>Purpose
The purpose of this document is to help users of Platform DXC APIs understand the standard API Service Contract. If you are planning to use a Platform DXC API, it is important that you understand the characteristics described in this document. 

Following are some of the key areas that a consumer would need to review initially as part of "PDXC API Service Contract":

- [Terms of Service](#terms-of-service)
- [Privacy](#privacy)
- [Deprecation Policy](#deprecation-policy)
- [Roadmap and Changelog](#roadmap)
- [Rate Limits](#rate-limit)

## <a href="#terms-of-service" id="#terms-of-service"></a>Terms of Service

- The use of PDXC APIs is currently restricted to the existing offerings of PDXC platform. 
- Some of the softwares required by or included in PDXC APIs may be offered under an open source license. Open source software licenses constitute separate written agreements. For certain APIs, open source software is listed in the documentation. To the limited extent the open source software license expressly supersedes the Terms.
- APIs designed and deployed for PDXC platform can be continuosly monitored and updated for any enhancement or defect fixation purpose by the Integration framework team.

[top](#purpose)

## <a href="#privacy" id="#privacy"></a>Privacy
The Platform DXC API's offer a "tool independent" mechanism for interacting with various service areas e.g. Event Management, Configuration Management, Request Fulfilment etc. The data attributes exchanged through the API Gateway are not stored but are transient. The API's rely on the security model of the target application to ensure that the data stored at rest is secured appropriately. Additionally, where there are specific policies surrounding data administration and data residence, these will not be enforced by the API Gateway. The policy will be enforced by the target application. The role of the API Gateway will be to route to appropriate instances of the target application.

[top](#purpose)

## <a href="#deprecation-policy" id="#deprecation-policy"></a>Deprecation Policy
The deprecation policies of an API, plays a significant role in the trust established as part of the API contract that API providers and consumers agree to. Whenever possible, restrict use of deprecated methods/classes. 

PDXC API deprecation policy supports APIs with `N` or `N-1` versions only where `N` is the current major version of an API available on the Integration framework. The supported Deprecated Version is 'N-1'. Applications developed using versions other than `N` or `N-1` will not be supported. When new version of an API is released, the release documentation should be updated to reflect the deprecation date for the previous version.

API Release is defined as follows:

| Release Type | Description
|:-----|:-----
| MAJOR | Given a release number MAJOR.MINOR.PATCH, MAJOR release is defined when any incompatible API changes are released.
MINOR |MINOR release is defined when any functionality is added in a backwards-compatible manner. Minor release is considered if substantial new functionality or improvements are introduced within the private code. It MAY include patch level changes. Patch version MUST be reset to 0 when minor release version is incremented.
PATCH |PATCH release is defined when backwards-compatible bug fixes are performed.A bug fix is defined as an internal change that fixes incorrect behavior.


[top](#purpose)

## <a href="#roadmap" id="#roadmap"></a>Roadmap and Changelog
With every major release, the PDXC API roadmap and changelog information should be properly documented and available in the release notes on Github. The reference to the Github changelog should be defined in the API info section. 

[top](#purpose)

## <a href="#rate-limit" id="#rate-limit"></a>Rate Limits

The default rate threshold for an API is 50 requests per second, with a burst rate of 25 requests. If this threshold is exceeded then a "Too Many Requests" response will be returned by the API Gateway. The sender is expected to retry their request at a later point in time.

[top](#purpose)
