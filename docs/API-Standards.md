# API Design Standards


## <a href="#document-purpose" id="document-purpose"></a>Document Purpose
The purpose of this document is to define the standards associated with a <b>Standard Platform DXC API</b> to be used across Platform DXC.  These standards will ensure that all Standard Platform DXC API's expose their functions consistently making them easier to consume within integrations

This document does not replace the DXC API Guidelines or OpenAPI specification. It supplements them by including additional Platform DXC API standards. API designers and developers should familiarize themselves with the following documents for better context of the content in this document:

* [API Design Guidelines](https://github.dxc.com/ArchitectureOffice/api-design-guidelines/blob/master/Guidelines.md)
* [Open API 2.0 specification](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)

><b>Note:</b> The standards described in this document should be adhered to in their entirety.  There are always exceptions to any rule.  If any of the defined standards cannot be adhered to, an exception needs to be raised and agreed.  To raise an exception enter your request into the Integration Standards Exceptions list [here](https://hpe.sharepoint.com/teams/stoola/CDA/Lists/Integration%20Standard%20Exceptions).  You will be invited to a weekly meeting to discuss the exception and rational for taking an alternative approach.  If you are unable to access the SharePoint list, or your request requires immediate focus, contact [Peter White](mailto:pwhite6@csc.com).


## Table of Contents
* [Document Purpose](#document-purpose)
* [API Resources](#api-resources)
	* [Resource Archetypes](#resource-archetypes)
	* [URL Structure](#url-structure)
	* [Resource Path Structure](#resource-path-structure)
	* [Paging](paging)
	* [HTTP Methods](#http-methods)
	* [Content Type](#media-type)
* [HTTP Header & Status Codes](#http-status-codes)
* [Tooling Standards](#tooling-standards)
* [API Repository](#api-repository)
* [REST API Security](#rest-api-security)
* [OpenAPI Schema Objects](#schema)
	* [Info Object](#info-object)
	* [Contact Object](#contact-object)
	* [License Object](#license-object)
	* [Path Object](#path-object)
	* [Definitions Object](#definitions-object)
	* [Operation Object](#operation-object)
	* [Request Body Object](#request-body-object)
	* [Responses Object](#responses-object)
	* [Tag Object](#tag-object)
	* [Patterned Objects](#patterned-objects)


## <a href="#api-resources" id="api-resources"></a>API Resources

The following sections describe in more detail design and usage considerations pertaining to resources.
* [Resource Archetypes](#resource-archetypes)
* [URL Structure](#url-structure)
* [Resource Path Structure](#path-structure)
* [Paging](#paging)
* [HTTP Methods](#http-methods)
* [Content Type](#media-type)

### <a href="#resource-archetypes" id="resource-archetypes"></a>Resource Archetypes

Standard DXC API's will accommodate the following resource types:
- Document Resources
- Collection Resources

**Note:** Other Resource Types may be included in future as need arises.

### <a href="#url-structure" id="url-structure"></a>URL Structure
Standard Platform DXC API's must adhere to the following URL structure:

`https://{server}/{environment}`

where,

`{server}` is the fully qualified hostname of the resource hosting the API. External facing port for the gateway is always 443, so no need to explicitly include the port

`{environment}` is a pointer to the specific instance of an API that aligns to a specific type of endpoint.  This must be one of
* *dev* ( for Development )
* *test* ( for Development Test )
* *qa* ( for Pre-production)
* *prod* ( for Production)


Examples,

https://gateway.dxc.com/test/dxc<br>
https://gateway.dxc.com/prod/microsoft


### Resource Path Structure

Platform DXC API's must follow the URI structure guidelines along with the following standards for resource path:

`{organization}/{mainEntityGroup}/{release}/{mainEntity}/{mainEntityID}/{operation}/{version}`

where,

`{organization}` signifies the owning Company of the API specification.  For all Platform DXC API's this should be _dxc_.  For vendor API's this should be an acronym associated with the vendor e.g. Microsoft could be _ms_.

`{mainEntityGroup}` is a reference to the Data Domain in the Enterprise Data Model to which the resource belongs e.g. Configuration Item belongs to Configuration Management.  Where the resource does not feature in the Enterprise Data Model it should describe the functional area e.g. Automation.  Where the Data Domain includes the word _management_ it should be stripped off e.g. *Incident Management* becomes *Incident*.  The mainEntityGroup label should be in the singular and be CamelCase e.g *incident*. **Note:** API's can be Application API's from another vendor, in which case mainEntityGroup should be the familiar name of the application (typically an acronym).

`{release}` represents the release number of the API. This should just be the major version of the release and must be reflected in the 'Version' tag of the 'Info Object' in the API specification document.  **Note:** Where the API is an Application API from another vendor, it should be the application version or release number.

`{mainEntity}` Represents the Logical Data Entity the resource really ties to e.g. incident.  Where the resource does not feature in the Enterprise Data Model it should describe the function e.g. action.  Typically the mainEntity should be in the singular if it represents a single resource, or in the plural if it represents multiple instances of the same resource.  e.g. _ci_ may be associated with a single Configuration Item vs. _cis_ which may be associated with multiple Configuration Items.  It must be in camelCase.

`{mainEntityId}` _Optional_. When the API Method requires an action to be taken against an existing resource, its identifier will need to be presented.  This is known as the mainEntityId in this structure.  The name of the mainEntityId must be camelCase for example eventID or actionId.

`{operation}` _Optional_. Where a specific operation is required against a resource that cannot be explained by an API Method or an attribute, it must appear on the resource path e.g. cancel.  It must be in camelCase.

`{version}` is the version of the resource. This is mandatory and must start with v1 and increment to v2, v3 etc.

>**Note:** During the course of building and testing an API, a resource can go through multiple modifications resulting in multiple versions of the resource. All of the versions of a resource will be retained, until it is time for the API to have a 'product release' at which time only the final version of each resource will be released with a final version number of v1 e.g. if the development process stops at v6, v6 will be v1 in the final release.
**Note:** All the resources in the next 'product release' of the API will start with v1 again. This concept applies to production patches as well.

examples,

__Automations API__

mainEntityGroup: *automation*
mainEntity: *action*

* `/dxc/automation/R1/action/v1` method `POST` would trigger an automation action on a target domain automation tool such as Raffia.
* `/dxc/automation/R1/action/123/cancel/v1` method `PUT` would update an existing automation action and cancel it.

__Configuration Items Bulk Load API__

mainEntityGroup: *configuration*  
mainEntity: *cis*

* `/dxc/configuration/R1/cis/v1` method `POST` would be used to create multiple Configuration Items in a target Configuration Management System.
* `/dxc/configuration/R1/cis/v1` method `PUT` would be used to update multiple Configuration Items in a target Configuration Management System.

__Service Request API__

mainEntityGroup: *request*  
mainEntity: *requestItem*

* `/dxc/request/R1/requestItem/v1` method `POST` would be used to create a new Request Item in a target Request Management System.
* `/dxc/request/R1/requestItem/1234/v1` method `GET` would be used to get information about an existing Request Item from a target Request Management System.


### <a href="paging" id="paging"></a>Paging

Platform DXC API's must use multiples of ten for defining the number of resources to be retrieved by a query.
The query parameters for paging when used, must be as follows:
* \_start
* \_num
* \_count

### <a href="#http-methods" id="http-methods"></a>HTTP Methods

Platform DXC API's can use one or more of the HTTP methods identified in the guidelines.
While OPTIONS is not needed, it is highly recommended, as it lays down the foundation for an API that meets or exceeds level 3 of the [Richardson Maturity Model](https://martinfowler.com/articles/richardsonMaturityModel.html).  

| HTTP Method | Usage
|:-----|:-----
| POST | For creating a new resource
PUT |For replacing an existing resource in its entirety
PATCH |For updating select attributes of a resource
GET |For retrieving resources in part or full

### <a href="#media-type" id="media-type"></a>Content type

Platform DXC API's must support content type of JSON. Other choices like XML are optional and could be built as needed.


## <a href="#http-status-codes" id="http-status-codes"></a>HTTP Header & Status Codes

Platform DXC API's must:

* use standard HTTP Status codes. Details on key status codes is available in the DXC API Guidelines document
* use HTTP Headers to enable content-based routing. The details for content based routing are available in the Getting Started Guide.
* use the following for defining any custom http headers:
	* The header parameter must be prefixed with "x-dxc-xxxx-", where the xxxx is meant to uniquely represent a service area.
	* Example: "x-dxc-intg-" Where 'intg' will be the unique id for the service area 'integration'


## <a href="#tooling-standards" id="tooling-standards"></a>Tooling Standards

Platform DXC API's will use the following tools

| Tool | Purpose
|:-----|:-----
| Swagger Editor | API editor for building and documenting API's. Developers can use JSON or YAML when documenting API's. However the version(s) loaded into the GitHub must be JSON.  [Instructions for downloading the Swagger Editor](./InstallSwaggerEditor.md)
Postman |For Testing API's. Downloaded latest version of [Postman](https://www.getpostman.con)
AWS API Gateway |For API lifecycle management
GitHub |For source control of API's. Both the JSON and the HTML for the API's must be loaded in GitHub


## <a href="#api-repository" id="api-repository"></a>API Repository

GitHub will be used as an API Repository and will hold the master specifications for each Standard Platform DXC API.  The GitHub folder and file names should adhere to the following standards:

### Folders

For each Service Area in Platform DXC, create a folder under /API's/pdxc to store all the API's for that service area.  The standard for the folder naming is as follows:

`/API's/pdxc/{service}-api`

where,

`{service}` represents the [Service Areas](https://github.dxc.com/GDO-CTO/Platform-DXC/blob/gh-pages/Services/Service_Catalog.md) as defined in the Platform DXC Service Catalog and should be in camelCase.  Instead of using the full service name from the Service Catalog, the following modifications should be made as appropriate:

* Remove the word Management, e.g. instead of 'Catalog Management', use the word 'catalog'
* If the service name is long, abbreviate it, e.g. instead of 'Definitive Software Library', use 'dsl'

examples,

* `/API's/pdxc/automation-api`
* `/API's/pdxc/configuration-api`
* `/API's/pdxc/dsl-api`


### Files

For each Service Area, there may be a need for multiple API's based on
* Logical data entities
* Logical grouping of functionality

Keeping the above in mind, the file naming standard is defined as follows:

`{component}-{function}`

where,

`{component}` is a _mandatory_ token, representing the logical data entity or logical function in camelCase.

`{function}` is an _optional_ token. It can further represent any specific function within the component.

examples,

* `/API's/pdxc/automations-api/automations.json`
* `/API's/pdxc/configuration-api/configurationItems-BulkLoad.json`
* `/API's/pdxc/configuration-api/configurationItems-Publish.json`
* `/API's/pdxc/integration-api/integrationSwagger.json`

>**Note:** Initially the raw JSON as produced by each service area must be loaded into GitHub. Once it has been reviewed by peers, validated and updated per gateway specific constraints, the enhanced version of the JSON should replace the original raw JSON in the GitHub.


## <a href="#rest-api-security" id="rest-api-security"></a>REST API HTTP Security Protocol

In addition to the API guidelines, Platform DXC API's must adhere to following HTTP security protocol rule(s):

* TLS 1.2 or greater should be required for all access to the API

### Authentication

Platform DXC API's must support one of the following standards for authentication:

* Basic Authentication (Base64 encoded password)
* OAUTH2

The authentication mechanism may be different for internal vs. external communications (via API gateway).


## <a href="#schema" id="schema"></a>OpenAPI Schema Objects

### <a href="#info-object" id="info-object"></a>Info Object
In addition to required 'Title' and 'Version' fields, Standard Platform DXC API's **must** include 'Description' field and 'Contact' object.
Title must be the API name but use space to separate words in the API Name, instead of camel case
Example: Title for the API 'configurationItems' must be 'Configuration Items'

### <a href="#contact-object" id="contact-object"></a>Contact Object
For a contact, Name and Email address are sufficient. URL is not necessary.

### <a href="#license-object" id="license-object"></a>License Object
Not used in Standard Platform DXC API's. If the need is identified in the future, the standards document will be updated.

### <a href="#path-object" id="path-object"></a>Path Object
For every path in the API specification, description tag **must** be included.

### <a href="#definitions-object" id="definitions-object"></a>Definitions Object
Standard Platform DXC API's must use the definitions objects where appropriate to reduce repetition. This must be applied at the very minimum for the following:
* schemas (both singular and plural) - use definitions object
* parameters - use parameter definitions object
* common errors - use response definitions object

Parameter Objects: In addition to the "required' tags ( name, type and in) mandated by the Open API Specifications 2.0, the following tags **must** be included in the API definition in order to better document the published API's
* description - A brief description of the parameter. It could optionally include simple examples. Also include if there is a further classification of data type. As an example, a parameter could be defined as type 'string', however it may actually be a date or date-time. Maximum length of the description should be 120 characters or less
* required - Identifies whether this parameter is mandatory or not

>**Note:** Model names should be defined in camel case for all definitions objects. Only alphanumeric characters allowed.

### <a href="#operation-object" id="operation-object"></a>Operation Object
Standard Platform DXC API's must adhere to the following rules:

* OperationId to be defined in camel case e.g., updateIncidentStatus
* It must include at least one Tag object per operations. All the operations performed on a resource must have this common tag e.g., the tag 'incident' should be listed under both GET and POST operationsof the incident.

### <a href="#request-body-object" id="request-body-object"></a>Request Body Object
Standard Platform DXC API's **must** support media type of JSON. Other choices like XML are optional and could be built as needed.

## <a href="#responses-object" id="responses-object"></a>Responses
Each operation **must** have at least one response defined in the API.

### <a href="#tag-object" id="tag-object"></a>Tag Object
Standard Platform DXC API's must include at least one Tag object per operation.  Each tag:
- should use the name of the logical entity name, e.g. incident, automation etc.
- should be re-used to group like operations

### <a href="#patterned-objects" id="patterned-objects"></a>Patterned Objects/Extensions
Extensions to the swagger schema using patterned objects and fields, if included must be prefixed with "x-dxc-"
