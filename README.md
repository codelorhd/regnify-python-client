# regnify-api
No description provided (generated by Openapi Generator https://github.com/openapitools/openapi-generator)

This Python package is automatically generated by the [OpenAPI Generator](https://openapi-generator.tech) project:

- API version: 1.0
- Package version: 1.0.5
- Build package: org.openapitools.codegen.languages.PythonClientCodegen

## Requirements.

Python &gt;&#x3D;3.7

## Migration from other generators like python and python-legacy

### Changes
1. This generator uses spec case for all (object) property names and parameter names.
    - So if the spec has a property name like camelCase, it will use camelCase rather than camel_case
    - So you will need to update how you input and read properties to use spec case
2. Endpoint parameters are stored in dictionaries to prevent collisions (explanation below)
    - So you will need to update how you pass data in to endpoints
3. Endpoint responses now include the original response, the deserialized response body, and (todo)the deserialized headers
    - So you will need to update your code to use response.body to access deserialized data
4. All validated data is instantiated in an instance that subclasses all validated Schema classes and Decimal/str/list/tuple/frozendict/NoneClass/BoolClass/bytes/io.FileIO
    - This means that you can use isinstance to check if a payload validated against a schema class
    - This means that no data will be of type None/True/False
        - ingested None will subclass NoneClass
        - ingested True will subclass BoolClass
        - ingested False will subclass BoolClass
        - So if you need to check is True/False/None, instead use instance.is_true_oapg()/.is_false_oapg()/.is_none_oapg()
5. All validated class instances are immutable except for ones based on io.File
    - This is because if properties were changed after validation, that validation would no longer apply
    - So no changing values or property values after a class has been instantiated
6. String + Number types with formats
    - String type data is stored as a string and if you need to access types based on its format like date,
    date-time, uuid, number etc then you will need to use accessor functions on the instance
    - type string + format: See .as_date_oapg, .as_datetime_oapg, .as_decimal_oapg, .as_uuid_oapg
    - type number + format: See .as_float_oapg, .as_int_oapg
    - this was done because openapi/json-schema defines constraints. string data may be type string with no format
    keyword in one schema, and include a format constraint in another schema
    - So if you need to access a string format based type, use as_date_oapg/as_datetime_oapg/as_decimal_oapg/as_uuid_oapg
    - So if you need to access a number format based type, use as_int_oapg/as_float_oapg
7. Property access on AnyType(type unset) or object(dict) schemas
    - Only required keys with valid python names are properties like .someProp and have type hints
    - All optional keys may not exist, so properties are not defined for them
    - One can access optional values with dict_instance['optionalProp'] and KeyError will be raised if it does not exist
    - Use get_item_oapg if you need a way to always get a value whether or not the key exists
        - If the key does not exist, schemas.unset is returned from calling dict_instance.get_item_oapg('optionalProp')
        - All required and optional keys have type hints for this method, and @typing.overload is used
        - A type hint is also generated for additionalProperties accessed using this method
    - So you will need to update you code to use some_instance['optionalProp'] to access optional property
    and additionalProperty values
8. The location of the api classes has changed
    - Api classes are located in your_package.apis.tags.some_api
    - This change was made to eliminate redundant code generation
    - Legacy generators generated the same endpoint twice if it had > 1 tag on it
    - This generator defines an endpoint in one class, then inherits that class to generate
      apis by tags and by paths
    - This change reduces code and allows quicker run time if you use the path apis
        - path apis are at your_package.apis.paths.some_path
    - Those apis will only load their needed models, which is less to load than all of the resources needed in a tag api
    - So you will need to update your import paths to the api classes

### Why are Oapg and _oapg used in class and method names?
Classes can have arbitrarily named properties set on them
Endpoints can have arbitrary operationId method names set
For those reasons, I use the prefix Oapg and _oapg to greatly reduce the likelihood of collisions
on protected + public classes/methods.
oapg stands for OpenApi Python Generator.

### Object property spec case
This was done because when payloads are ingested, they can be validated against N number of schemas.
If the input signature used a different property name then that has mutated the payload.
So SchemaA and SchemaB must both see the camelCase spec named variable.
Also it is possible to send in two properties, named camelCase and camel_case in the same payload.
That use case should be support so spec case is used.

### Parameter spec case
Parameters can be included in different locations including:
- query
- path
- header
- cookie

Any of those parameters could use the same parameter names, so if every parameter
was included as an endpoint parameter in a function signature, they would collide.
For that reason, each of those inputs have been separated out into separate typed dictionaries:
- query_params
- path_params
- header_params
- cookie_params

So when updating your code, you will need to pass endpoint parameters in using those
dictionaries.

### Endpoint responses
Endpoint responses have been enriched to now include more information.
Any response reom an endpoint will now include the following properties:
response: urllib3.HTTPResponse
body: typing.Union[Unset, Schema]
headers: typing.Union[Unset, TODO]
Note: response header deserialization has not yet been added


## Installation & Usage
### pip install

If the python package is hosted on a repository, you can install directly using:

```sh
pip install git+https://github.com/GIT_USER_ID/GIT_REPO_ID.git
```
(you may need to run `pip` with root permission: `sudo pip install git+https://github.com/GIT_USER_ID/GIT_REPO_ID.git`)

Then import the package:
```python
import regnify
```

### Setuptools

Install via [Setuptools](http://pypi.python.org/pypi/setuptools).

```sh
python setup.py install --user
```
(or `sudo python setup.py install` to install the package for all users)

Then import the package:
```python
import regnify
```

## Getting Started

Please follow the [installation procedure](#installation--usage) and then run the following:

```python

import time
import regnify
from pprint import pprint
from regnify.apis.tags import auth_api
from regnify.model.access_token import AccessToken
from regnify.model.body_login_token_post import BodyLoginTokenPost
from regnify.model.http_validation_error import HTTPValidationError
# Defining the host is optional and defaults to http://localhost
# See configuration.py for a list of all supported configuration parameters.
configuration = regnify.Configuration(
    host = "http://localhost"
)


# Enter a context with an instance of the API client
with regnify.ApiClient(configuration) as api_client:
    # Create an instance of the API class
    api_instance = auth_api.AuthApi(api_client)
    body = dict(
        grant_type="password",
        username="username_example",
        password="password_example",
        scope="",
        client_id="client_id_example",
        client_secret="client_secret_example",
    ) # {str: (bool, date, datetime, dict, float, int, list, str, none_type)} |  (optional)

    try:
        # Login
        api_response = api_instance.login(body=body)
        pprint(api_response)
    except regnify.ApiException as e:
        print("Exception when calling AuthApi->login: %s\n" % e)
```

## Documentation for API Endpoints

All URIs are relative to *http://localhost*

Class | Method | HTTP request | Description
------------ | ------------- | ------------- | -------------
*AuthApi* | [**login**](docs/apis/tags/AuthApi.md#login) | **post** /token | Login
*RolesAndPermissionsApi* | [**assign_role**](docs/apis/tags/RolesAndPermissionsApi.md#assign_role) | **post** /roles/{role_id}/assign-role | Assign Role
*RolesAndPermissionsApi* | [**create_role**](docs/apis/tags/RolesAndPermissionsApi.md#create_role) | **post** /roles/ | Create Role
*RolesAndPermissionsApi* | [**delete_role**](docs/apis/tags/RolesAndPermissionsApi.md#delete_role) | **delete** /roles/{role_id} | Delete Role
*RolesAndPermissionsApi* | [**edit_role**](docs/apis/tags/RolesAndPermissionsApi.md#edit_role) | **put** /roles/{role_id} | Edit Role
*RolesAndPermissionsApi* | [**get_role**](docs/apis/tags/RolesAndPermissionsApi.md#get_role) | **get** /roles/{role_id} | Get Role
*RolesAndPermissionsApi* | [**get_roles**](docs/apis/tags/RolesAndPermissionsApi.md#get_roles) | **get** /roles/ | Get Roles
*RolesAndPermissionsApi* | [**list_users_assigned_to_role**](docs/apis/tags/RolesAndPermissionsApi.md#list_users_assigned_to_role) | **get** /roles/{role_id}/list-assigned-users | List Users Assigned To Role
*RolesAndPermissionsApi* | [**unassign_role**](docs/apis/tags/RolesAndPermissionsApi.md#unassign_role) | **post** /roles/{role_id}/unassign-role | Unassign Role
*UsersApi* | [**admin_change_user_password**](docs/apis/tags/UsersApi.md#admin_change_user_password) | **put** /users/{user_id}/admin-change-user-password | Admin Change User Password
*UsersApi* | [**change_user_password**](docs/apis/tags/UsersApi.md#change_user_password) | **put** /users/change-user-password | Change User Password
*UsersApi* | [**create_user**](docs/apis/tags/UsersApi.md#create_user) | **post** /users/ | Create User
*UsersApi* | [**list_scopes**](docs/apis/tags/UsersApi.md#list_scopes) | **get** /users/list-scopes | List Scopes
*UsersApi* | [**read_user**](docs/apis/tags/UsersApi.md#read_user) | **get** /users/{user_id} | Read User
*UsersApi* | [**read_user_me**](docs/apis/tags/UsersApi.md#read_user_me) | **get** /users/token | Read User Me
*UsersApi* | [**read_users**](docs/apis/tags/UsersApi.md#read_users) | **get** /users/ | Read Users
*UsersApi* | [**request_password_change**](docs/apis/tags/UsersApi.md#request_password_change) | **post** /users/request-password-change | Request Password Change
*UsersApi* | [**resend_invite**](docs/apis/tags/UsersApi.md#resend_invite) | **post** /users/resend-invite | Resend Invite
*UsersApi* | [**update_user**](docs/apis/tags/UsersApi.md#update_user) | **put** /users/{user_id} | Update User

## Documentation For Models

 - [AccessToken](docs/models/AccessToken.md)
 - [AppResponseModel](docs/models/AppResponseModel.md)
 - [BodyEditRoleRolesRoleIdPut](docs/models/BodyEditRoleRolesRoleIdPut.md)
 - [ChangePassword](docs/models/ChangePassword.md)
 - [ChangePasswordWithToken](docs/models/ChangePasswordWithToken.md)
 - [HTTPValidationError](docs/models/HTTPValidationError.md)
 - [ManyRolesOut](docs/models/ManyRolesOut.md)
 - [ManySystemScopeOut](docs/models/ManySystemScopeOut.md)
 - [ManyUserRolesOut](docs/models/ManyUserRolesOut.md)
 - [ManyUsersInDB](docs/models/ManyUsersInDB.md)
 - [MiniRoleOut](docs/models/MiniRoleOut.md)
 - [MiniUserRoleOut](docs/models/MiniUserRoleOut.md)
 - [OrderBy](docs/models/OrderBy.md)
 - [OrderDirection](docs/models/OrderDirection.md)
 - [ProfileOut](docs/models/ProfileOut.md)
 - [RoleCreate](docs/models/RoleCreate.md)
 - [RoleOut](docs/models/RoleOut.md)
 - [SystemScopeOut](docs/models/SystemScopeOut.md)
 - [UserCreate](docs/models/UserCreate.md)
 - [UserOut](docs/models/UserOut.md)
 - [UserRoleOut](docs/models/UserRoleOut.md)
 - [UserUpdate](docs/models/UserUpdate.md)
 - [ValidationError](docs/models/ValidationError.md)

## Documentation For Authorization

 Authentication schemes defined for the API:
## OAuth2PasswordBearer

- **Type**: OAuth
- **Flow**: password
- **Authorization URL**: 
- **Scopes**: 
 - **me**: Read information about the current user.


## Author

admin@regnify.com
admin@regnify.com
admin@regnify.com

## Notes for Large OpenAPI documents
If the OpenAPI document is large, imports in regnify.apis and regnify.models may fail with a
RecursionError indicating the maximum recursion limit has been exceeded. In that case, there are a couple of solutions:

Solution 1:
Use specific imports for apis and models like:
- `from regnify.apis.default_api import DefaultApi`
- `from regnify.model.pet import Pet`

Solution 1:
Before importing the package, adjust the maximum recursion limit as shown below:
```
import sys
sys.setrecursionlimit(1500)
import regnify
from regnify.apis import *
from regnify.models import *
```
