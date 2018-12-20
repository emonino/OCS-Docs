---
uid: omfIngressPublishers
---

Publishers
==================

A Publisher is a logical construct which is used by OSIsoft Cloud Services (OCS) to authenticate 
and categorize OSIsoft Message Format (OMF) messages. After creating a publisher using the API, 
a user can then generate a security token for that publisher. The token must be added to the headers 
of OMF messages that are sent to OCS. If a publisher is deleted, all security tokens generated 
for the Publisher become invalid. Individual tokens can also be deleted to make them invalid.

While OMF messages may be sent to OCS immediately after provisioning a publisher, the messages 
cannot be made available for consumption until a Topic is created. However, some 
messages are stored for later consumption in OCS databases. See OMF documentation for more information. 

The API calls in this section are all used to manage publishers.

Tokens
------

After creating a publisher, security tokens for that publisher can be created. These tokens are 
a type of bearer token, which means that any client that presents the token will be able to 
authenticate as that publisher, assuming the token is valid. Because of this, it is imperative 
that the token be stored in a secure location that is accessible only to the application that 
is sending data. If the security token is ever compromised, it is possible for an unauthorized 
client to impersonate the publisher until the token expires or is deleted. 

The security token contains information that uniquely 
identifies the publisher. The following information is contained in the token: 

  * Token Id: A unique Id for the token. The Id is generated by the API. 
  * Publisher Id: The Id of the publisher this token is for.
  * Tenant Id: This Id identifies the owner of the publisher. 
  * Expiration Time: This determines when the security token expires. 
  * Signature: This is a cryptographic signature that verifies that the token is legitimate 
    and has not been tampered with. The signature uses the HMAC-SHA256 algorithm. 

The security token should never be transported over an unsecure network connection. If, for example, 
you have a separate management service that performs token registration and renewal with the 
API and then pushes the token down to the publisher that generates OMF messages, you must ensure that
your network connection to the publisher uses SSL/TLS or another secure protocol. 

Because each OMF message contains the token, never send OMF data to a service that does not 
implement SSL/TLS or other secure protocol. It is recommended that you keep token expiration times 
short and to have your application renew the token before it expires. For example, you could 
specify that the token expires after 24 hours and have your application renew the token every 
12 hours. By following this example, if the token is compromised, you limit the amount of time 
the token is valid. 

Data Models 
-----------

Publisher information is contained in an object called ``Publisher`` and has the following format: 

| Property        | Type                    | Details                                |
|-----------------|-------------------------|----------------------------------------|
| TenantId        | string                  | Identifies the owner of the publisher. |
| Id              | string                  | A unique ID generated by the API when  |
|                 |                         | the publisher is created.              |
| Name            | string                  | A friendly name for the publisher.     |
| Description     | string                  | A description for the publisher.       |
| CreationDate    | string                  | The time that the Publisher was        |
|                 |                         | created. The string is formatted using |
|                 |                         | ISO 8601 format.                       |

Token information is contained in an object called ``Token`` and has the following format: 

| Property        | Type                    | Details                                |
|-----------------|-------------------------|----------------------------------------|
| Id              | string                  | A unique ID generated by the API when  |
|                 |                         | the token is created.                  |
| PublisherId     | string                  | Identifies the publisher this token    |
|                 |                         | belongs to.                            |
| TokenString     | string                  | A set of claims along with a           |
|                 |                         | cryptographic signature that verifies  |
|                 |                         | that the token is legitimate and has   |
|                 |                         | not been tampered with. It is used     |
|                 |                         | when building OMF messages that are    |
|                 |                         | sent to the OCS.                       |
| CreationDate    | string                  | The time that the Token was created.   |
|                 |                         | The string is formatted using ISO 8601 |
|                 |                         | format.                                |
| ExpirationDate  | string                  | The time that the Token will expire.   |
|                 |                         | The string is formatted using ISO 8601 |
|                 |                         | format.                                |
| IsDeleted       | boolean                 | If a Token is revoked, any clients     |
|                 |                         | using that token are prevented from    |
|                 |                         | sending data.                          |


***********************

``GET api/tenants/{tenantId}/publishers/count``
--------------------------------------------

Returns the number of publishers assigned for a specified tenant.  


**Parameters**

``tenantId``
  A unique Id for the Tenant


**Returns**

Integer count of the number of publishers found. 

***********************

``GET api/tenants/{tenantId}/publishers``
--------------------------------------------

Returns all publishers for a tenant. 

**Parameters**

``tenantId``
  Unique Id for the tenant. 

**Returns**

  A list of Publisher objects found. 

************************

``GET api/tenants/{tenantId}/publishers/{publisherId}``
--------------------------------------------

Get a specific publisher. 

**Parameters**

``tenantId``
 Unique Id for the tenant. 
``publisherId``
  Unique Id for the publisher. 

**Returns**

  The Publisher object found.  

***************************

``GET api/tenants/{tenantId}/accesscontrol/publishers``
--------------------------------------------

Get the Access Control List that is used by default for new publishers.

**Parameters**

``tenantId``
  Unique Id for the tenant. 

**Returns**

An AccessControlList object.

***************************

``GET api/tenants/{tenantId}/publishers/{publisherId}/accesscontrol``
--------------------------------------------

Get the Access Control List for a specific publisher.

**Parameters**

``tenantId``
  Unique Id for the tenant. 
``publisherId``
  Unique Id for the publisher.  

**Returns**

An AccessControlList object.

***************************

``GET api/tenants/{tenantId}/publishers/{publisherId}/tokens``
--------------------------------------------

Get all tokens for a publisher.

**Parameters**

``tenantId``
 Unique Id for the tenant. 
``publisherId``
  Unique Id for the publisher. 

**Returns**

An array of Token objects.   

***************************

``GET api/tenants/{tenantId}/publishers/{publisherId}/tokens/{tokenId}``
--------------------------------------------

Get a specific token

**Parameters**

``tenantId``
 Unique Id for the tenant. 
``publisherId``
  Unique Id for the publisher. 
``tokenId``
  Unique Id for the token.

**Returns**

  The token object found. 

***************************

``POST api/tenants/{tenantId}/publisher``
-------------------------------------

Creates or updates a publisher. Only the name and description of a publisher can be updated.

Parameters: 

``tenantId``
  Unique Id for the tenant. 

**Body**

A Publisher object.  

**Returns**

  A Publisher object. 

******************************

``POST api/tenants/{tenantId}/publishers/{publisherId}/tokens``
--------------------------------------------

Create or undelete a token. If the token object provided does not have a tokenId, a new token is created.
Otherwise, specify the id of an existing deleted token that isn't expired, and it will be undeleted.

**Parameters**

``tenantId``
 Unique Id for the tenant. 
``publisherId``
 Unique Id for the publisher. 

 **Body**

A Token object.  

**Returns**

A Token object. 

***************************

``PUT api/tenants/{tenantId}/accesscontrol/publishers``
---------------------------------------

Update the default Access Control List for new publishers.

**Parameters**

``tenantId``
  Unique Id for the tenant. 

**Body**
  An AccessControlList object.
  
***************************

``PUT tenants/{tenantId}/publishers/{publisherId}/accesscontrol``
---------------------------------------

Update the Access Control List for a particular publisher.

**Parameters**

``tenantId``
  Unique Id for the tenant. 
``publisherId``
  Unique Id for the publisher. 

**Body**
  An AccessControlList object.
  
***************************

``DELETE api/tenants/{tenantId}/publishers/{publisherId}``
---------------------------------------------------

Deletes a publisher. All tokens for that publisher are deleted as well. 

**Parameters**

``tenantId`` 
  Unique Id for the tenant. 
``publisherId``
  Unique Id for the publisher. 

********************************

``DELETE api/tenants/{tenantId}/publishers/{publisherId}/tokens/{tokenId}``
---------------------------------------------------

Deletes a token.

**Parameters**

``tenantId`` 
  Unique Id for the tenant. 
``publisherId``
  Unique Id for the publisher. 
``tokenId``
  Unique Id for the token. 

**Returns**

A Token object for the deleted token. 

********************************