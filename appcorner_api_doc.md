# APPCORNER API

## Index
### [1. Security](#security) <br>
### [2. User Management](#user-management) <br>
### [3. Location services](#location-services) <br>
### [4. Messaging services](#messaging-services) <br>
### [5. Real time messages: RTA](#real-time-availability) <br>
### [6. Push Notifications](#push-notifications) <br>
### [7. Profile](#profile) <br>
### [8. Error Handling](#error-handling) <br>
### [9. Test Users](#test-users) <br>
### [10. Admin Endpoints](#admin) <br>

## Information

### Available endpoints

| ENV  |  URL  | 
|:-:|:-:|
| PRO  |  https://{{URL}}/api/v1/ | 

# API Information

## Security

### Client authentication

All the request should provide the header `x-api-token` to connect to the API with a valid API key.


### Login authentication

All the request outside the scope of `/user` should provide a valid token provided from `/user/authenticate` or 
`/user/registration`.

The token should be sended with one of the next options:

#### Key `x-access-token` in headers

Just send the token as a header with the key `x-access-token`.

#### Value `token` in query

Just send the token as a value with the key `token` as a query parameter.

#### Value `token` in body

Just send the token as a value with the key `token` as a body parameter.

-----

## Entity Models

### UserModel

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:-:|
| identifier  | Yes  | String | Internal (API generated) user identifier |
| alias  | Yes  | String | User alias  |
| deviceToken   | Yes | String | Device token used only at registration |
| scope   | Yes | Int | [See `UserScopeType`](#userscopetype)|
|location| Yes |  LocationModel | [See `LocationModel`](#locationmodel)|
| rating  | Yes  | Double | User rating (API Generated) |
| blockedUsers  | No  | [String] | Array of ids of all the users blocked (API Generated) |
| status  | No  | Int | 0: The user is blocked, 1: the user is standard |
| description  | No  | String | Optional field to describe user data |


Example (RAW model never exposed): 

````json
{
    "identifier": "a9f78425-a441-485c-bb99-40acc564fc6a",
    "alias": "Dummy",
    "deviceToken": "iOS_1231-231-23-123-123-123",
    "scope": 0,
    "status" : 1,
    "location": {
		"coordinates": [-73.856077, 40.848447]
	},
    "rating": 4.90,
    "description": "I want to help"
}
````

Example from a API request: 

````json
    {
        "alias": "UserClot",
        "scope": 1,
        "location": [
            41.4105,
            2.186642
        ],
        "identifier": "5e839c91beb9170f4ac100af",
        "rating": 0
    }
````

#### UserScopeType

|Value | Name  |  Description  |
|:-:|:-:|:-:|
|0| `ProvideHelp`  | User that wants to provide help |
|1| `GetHelp`  | User that wants to be helped |

### LocationModel

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:-:|
| coordinates  | Yes  | [Number] | Array with the coordinates |

Array with a location:
- 0: longitude
- 1: latitude

Example:
````json
{
    "coordinates": [-73.856077, 40.848447]
}
````

# User Management

## Authentication

Performs a login authentication.

> /user/authenticate

@POST <br>
`/user/authenticate`

### Entry parameters

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:-:|
| alias  | Yes  | String | User alias  |
| deviceToken   | Yes | String | Device token provided at registration |

### Response parameters

| Name  |  Type |   Description  |
|:-:|:-:|:-:|
| profile  | [UserModel](#usermodel) |  Returns the user profile info |
| token  | String | JSONWebtoken with the required information to perform future calls  |

Invocation example
@POST
`````json
https://{{URL}}/api/v1/user/authenticate

{
    "alias": "test",
    "deviceToken": "123456"
}
`````

Response

`````json
{
    "profile": {
        "alias": "UserClotHelps",
        "scope": 0,
        "location": [
            41.4105,
            2.186642
        ],
        "description": "Test",
        "createdAt": "2020-04-18T14:32:55.939Z",
        "updatedAt": "2020-04-18T14:32:55.939Z",
        "identifier": "5e9b0f97c0c5ac2e95e64dbb"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGlmaWVyIjoiNWU5YjBmOTdjMGM1YWMyZTk1ZTY0ZGJiIiwiYWxpYXMiOiJVc2VyQ2xvdEhlbHBzIiwiaWF0IjoxNTg3MzAwOTY4LCJleHAiOjQ3NDMwNjA5Njh9.DB-hsy17nzmkeW3eDm8ueH6TDok8eG5gJ6vN_WbjQZM"
}
`````
> See [Error Handling](#error-handling) to provide an accurate client response with the codes 004 and 005.

## Register

Performs a registration.

> /user/register

@POST <br>
`/user/register`

### Entry parameters

Send a UserModel without the API generated fields <br>
[UserModel](#usermodel)

### Response parameters

| Name  |  Type |   Description  |
|:-:|:-:|:-:|
| profile  | [UserModel](#usermodel) |  Returns the user profile info |
| token  | String | JSONWebtoken with the required information to perform future calls  |

Invocation example <br>

@POST <br>
https://{{URL}}/api/v1/user/register
````json
{
	"alias": "Test B",
	"deviceToken": "1234",
	"scope": 1,
	"location": {
		"coordinates": [1,2]
	},
	"description": "Test"
}
`````

Response

`````json
{
    "profile": {
        "alias": "Postman",
        "scope": 1,
        "location": [
            1,
            2
        ],
        "description": "Test",
        "createdAt": "2020-04-19T12:56:51.721Z",
        "updatedAt": "2020-04-19T12:56:51.721Z",
        "identifier": "5e9c4a935a943a0fe3a0bda2"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGlmaWVyIjoiNWU5YzRhOTM1YTk0M2EwZmUzYTBiZGEyIiwiYWxpYXMiOiJQb3N0bWFuIiwiaWF0IjoxNTg3MzAxMDExLCJleHAiOjQ3NDMwNjEwMTF9.TwnYFlXerk13x3_WhcwABZkUEmUcEbDP-tMbB5AOBBQ"
}
`````

## Validate

Checks if the sesion is valid -TEST ONLY- DO NOT USE

> /validate

@POST <br>
`/validate`

`````json
@(200)
{
    "OK"
}
`````

# Location Services

## Fetch near users

Fetch users with the opposite scope and ordered by location

> /location/nearUsers

@GET <br>
`/location/nearUsers`

### Entry parameters

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:-:|
| numberOfItems  | No  | Int | Max. number of locations to be returned (default 20). Set to `0` to return all  |
| maxDistance  | No  | Double | Max. distance to search (in meters)  |

### Response parameters

Ordered array of UsersModel<br>
[UserModel](#usermodel)
Without the fields: [`deciveToken`]

Invocation example <br>


@GET <br>
> https://{{URL}}/api/v1/location/nearUsers?numberOfItems=10&maxDistance=800

Response

`````json
[
    {
        "alias": "UserClot",
        "scope": 1,
        "location": [
            41.4105,
            2.186642
        ],
        "identifier": "5e839c91beb9170f4ac100af",
        "rating": 0
    },
    {
        "alias": "UserGlories",
        "scope": 1,
        "location": [
            41.404631,
            2.189944
        ],
        "identifier": "5e839c92beb9170f4ac100b3",
        "rating": 0
    },
    {
        "alias": "UserLlacuna",
        "scope": 1,
        "location": [
            41.399347,
            2.197575
        ],
        "identifier": "5e839c91beb9170f4ac100b1",
        "rating": 0
    }
]
`````

# Messaging Services

## MessageModel

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:-:|
| id  | Yes  | String | Internal (API generated) message identifier |
| senderId  | Yes  | String | User identifier sender of the message  |
| receiverId  | Yes  | String | User identifier receiver of the message  |
| conversationId  | Yes  | String | Unique conversation identifier |
| type   | Yes | Int | [See `MessageType`](#messagetype)|
| value  | Yes  | String | Value of the message if type "1" is the text |
| payload  | No  | String | Optional string to storage data (limited size) |
| timestamp  | Yes  | Int | Standard Epoch unix timestamp of the message [More info](https://www.unixtimestamp.com/index.php) |

> Example

````json
{
    "type": 1,
    "value": "Hola",
    "payload": "a",
    "senderId": "5e839dd989138c100ac2e60a",
    "receiverId": "5e839c91beb9170f4ac100af",
    "timeStamp": 1585859008.387,
    "conversationId": "5e839c91beb9170f4ac100af5e839dd989138c100ac2e60a",
    "id": "5e8649c0d388cb088cc8a85e"
}
````

### MessageType

|Value | Name  |  Description  |
|:-:|:-:|:--|
|0| `SystemMessage`  | Message sended by the system (TBA) |
|1| `TextMessage`  | Message of type text |

## Send Message

Sends a text message to a registered user. <br>
**PUT** Request

> /messages

@PUT <br>
`/messages`

### Entry parameters

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:--|
| message  | Yes  | String | Text of the message  |
| receiverIdentifier  | Yes  | String | Receiver user identifier  |
| payload  | No  | String | Optional data to add to the message  |

### Response parameters

The new message created<br>
[MessageModel](#messagemodel)

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:-:|
| *  | Yes  | [MessageModel](#messagemodel) | The message data |
| isReceiverOnline  | **No**  | Bool | Returns true if the receiver is online when the message is sended |

Invocation example: <br>

@PUT <br>
> https://{{URL}}/api/v1/messages

````json
{
	"message": "Hola",
	"receiverIdentifier" : "5e839c91beb9170f4ac100af",
	"payload" : "a"
}
````

Response example:

````json
{
    "type": 1,
    "value": "Prueba mensaje postman",
    "payload": "a",
    "senderId": "5e9b0f97c0c5ac2e95e64dbb",
    "receiverId": "5e9c4d8f7002c93ae9764071",
    "timeStamp": 1587303798.879,
    "conversationId": "5e9b0f97c0c5ac2e95e64dbb5e9c4d8f7002c93ae9764071",
    "id": "5e9c557604ad801328ac72d6",
    "isReceiverOnline": false
}
````

## Retrieve messages

Fetchs all the pending messages for the logged user.

> <b>Important</b> after fetching all the message you must call the service `/ack` to inform that the messages are received.

### Entry parameters

(None)

### Response parameters

Array of MessageModel<br>
[UserModel](#messagemodel)

Invocation example: <br>

@GET <br>
> https://{{URL}}/api/v1/messages

Response example:

````json
[
    {
        "type": 1,
        "value": "Hola",
        "senderId": "5e839dd989138c100ac2e60a",
        "receiverId": "5e839c91beb9170f4ac100af",
        "timeStamp": 1585858517.061,
        "conversationId": "5e839c91beb9170f4ac100af5e839dd989138c100ac2e60a",
        "id": "5e8647d52e8e8707a28b49a3"
    },
    {
        "type": 1,
        "value": "Hola",
        "payload": "a",
        "senderId": "5e839dd989138c100ac2e60a",
        "receiverId": "5e839c91beb9170f4ac100af",
        "timeStamp": 1585858717.664,
        "conversationId": "5e839c91beb9170f4ac100af5e839dd989138c100ac2e60a",
        "id": "5e86489de1b5f307d38f8b24"
    },
    {
        "type": 1,
        "value": "Hola",
        "payload": "a",
        "senderId": "5e839dd989138c100ac2e60a",
        "receiverId": "5e839c91beb9170f4ac100af",
        "timeStamp": 1585858802.618,
        "conversationId": "5e839c91beb9170f4ac100af5e839dd989138c100ac2e60a",
        "id": "5e8648f2e1b5f307d38f8b25"
    },
    {
        "type": 1,
        "value": "Hola",
        "payload": "a",
        "senderId": "5e839dd989138c100ac2e60a",
        "receiverId": "5e839c91beb9170f4ac100af",
        "timeStamp": 1585858830.483,
        "conversationId": "5e839c91beb9170f4ac100af5e839dd989138c100ac2e60a",
        "id": "5e86490e64527e07fe631e1a"
    },
    {
        "type": 1,
        "value": "Hola",
        "payload": "a",
        "senderId": "5e839dd989138c100ac2e60a",
        "receiverId": "5e839c91beb9170f4ac100af",
        "timeStamp": 1585858832.035,
        "conversationId": "5e839c91beb9170f4ac100af5e839dd989138c100ac2e60a",
        "id": "5e86491064527e07fe631e1b"
    }
]
````

## Acknowledge received messages

Informs that the client has received the messages defined in the field `identifiers` <br>
**POST** Request

> /messages

@POST <br>
`/messages/ack`

### Entry parameters

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:--|
| identifiers  | Yes  | Array(String) | Array of all the message identifiers that the user has received |


### Response

(None)

Invocation example: <br>

@POST <br>
> https://{{URL}}/api/v1/messages/ack

````json
{
	"identifiers": ["5e84906bd4564d12899f1496",  "5e847b5219e72b0c3d8cf067"]
}
````

Response example:

@200
````json
{

}
````

## Real Time availability

The server, using SSE can send messages to the clients, [See SSE](https://en.wikipedia.org/wiki/Server-sent_events).

> /rta

@GET <br>
`/rta`

#### Sened events

|Value | Description  |
|:-:|:--|
| "new_user_message"| A new message from an user  |
| "new_system_message" | A new message from the System (#TBA) |

#### System Messages

Used to inform the client about internal messages. All the messages will be plain text with one of these values:

|Value | Description  |
|:-:|:--|
| "terminate_connection" | When received the client should terminate all SSE connection with the server immediately. |


##### New user message

Sended when the client (logged user) has a new message.

The data is a message model: [MessageModel](#messagemodel)

> Please remember to call the service `/ack` with the received messages to remove the message from the server.

### Entry parameters

For debug you can send the query param `testMode`= true to receive test messages to validate the client connection. You can also provide the field `numberOfEvents` to send a max. amount of events.

### Response

>{{url}}/api/v1/rta?testMode=true&numberOfEvents=2
````json

data: "Default test message 0"

event: new_user_message
data: "Custom event test Message"

event: new_system_message
data: {"a":1,"b":false,"c":"test","d":{"a":1},"e":[1,2,"a"]}

event: Custom event test Message
data: "new_text"

data: "Default test message 1"

event: new_user_message
data: "Custom event test Message"

event: new_system_message
data: {"a":1,"b":false,"c":"test","d":{"a":1},"e":[1,2,"a"]}

event: Custom event test Message
data: "new_text"
````

## Push Notifications

The client will receive a push notification with the message using the push service [OneSignal](https://documentation.onesignal.com/docs).

In order to receive the push notification all the clients must register the device with the tags: (see the guides on how to send a tag)

|Value | Description  |
|:-:|:--|
| "userguid"| Unique user identifier provided by the api after an authentication or registration  |

The server only sends the messages using push with a client (identifier with their `identifier` key) that is not connected with SSE service.

> iOS Guide

[Quick Start iOS](https://documentation.onesignal.com/docs/ios-sdk-setup)

[iOS SDK Documentation](https://documentation.onesignal.com/docs/ios-native-sdk)

[iOS SDK send a TAG](https://documentation.onesignal.com/docs/ios-native-sdk#section-send-tag)

- Example:

````swift
OneSignal.sendTag("userguid", value: "5e9b0f97c0c5ac2e95e64dbb")
````

> Android Guide

[Quit Start Android](https://documentation.onesignal.com/docs/android-sdk-setup)

[Android SDK Documentation](https://documentation.onesignal.com/docs/android-native-sdk)

[Android SDK send a TAG](https://documentation.onesignal.com/docs/android-native-sdk#section-send-tag)

- Example:

````swift
OneSignal.sendTag("userguid", value: "5e9b0f97c0c5ac2e95e64dbb")
````

# Profile
Allows client user profile management and access.
/profile

## Get user logged profile

Returns the logged user profile <br>
**GET** Request

> /profile

@PUT <br>
`/profile`

### Entry parameters

- NONE -

### Response

[UserModel](#usermodel)

Invocation example: <br>

@GET <br>
> https://{{URL}}/api/v1/profile

````json

````

Response example:

@200
````json
{
    "alias": "UserClotHelps",
    "scope": 0,
    "location": [
        41.4105,
        2.186642
    ],
    "description": "Test",
    "createdAt": "2020-03-31T19:45:29.175Z",
    "updatedAt": "2020-03-31T19:45:29.175Z",
    "identifier": "5e839dd989138c100ac2e60a"
}
````

## Get public profile

Returns a public user profile <br>
**GET** Request

> /profile/:profileIdentifier

@GET <br>
`/profile/5e839c91beb9170f4ac100af`

### Entry parameters

PathParam:<br>
`profileIdentifier`: identifier of the profile to retrieve.

### Response

Public UserModel
[UserModel](#usermodel)

Invocation example: <br>

@GET <br>
> https://{{URL}}/api/v1/profile/5e839c91beb9170f4ac100af

````json

````

Response example:

@200
````json
{
    "alias": "UserClot",
    "scope": 1,
    "location": [
        41.4105,
        2.186642
    ],
    "description": "Test",
    "identifier": "5e839c91beb9170f4ac100af"
}
````

------

## Error handling
### HTTP Status Codes
| HTTP Status Code  |   Description  |
|:-:|:--|
| 400 | Sended when a logic error happens. [See Logic Error](#logic-error) for detailed information.  |
| 401 | Sended when the auth token is invalid |
| 403 | Sended when the API token is invalid |
| 500 | Sended after an unexpected server error  |

### Logic Error

If an 400 is returned probably the server will return an "errorCode" (identifier of the error) and an "errorMessage" with the contextualized error.

> List of Logic Error

| Error Code  |   Description  |
|:-:|:--|
| -200 | Invalid API Key |
| -100 | Errors related with the security |
| -101 | The authtoken has an invalid userIdentifier reference (maybe the identifier provided doesn't exists in the DB, perform a new registration) |
| 000 | Unknown error |
| 001 | When the user provided in the request doesn't exists |
| 002 | When the user that performs an action to another user cannot be the same user |
| 003 | When already exists an user with the provided alias |
| 004 | When the client (pretoken authentication) failed (returned when in /authenticate the user with the alias provided is not found). |
| 005 | When the user pretoken authentication failed (returned when in /authenticate the password/deviceToken didn't match with the provided) |
| 100 | Some of the params required to fullfil the request are missing |
| 101 | Some of the params required to fullfil the request are invalid |

> Example response HTTP: 400

````json
{
    "errorCode": "002",
    "errorMessage" : "The user is invalid"
}
````

## Test Users

Only available on DEV enviorenment.

| Alias | DeviceToken  |   Description |
|:-:|:-:|:-:|
| UserClotHelps | 1234  |   User located at Clot station (Barcelona) with the scope `ProvideHelp` it should return a valid test set for the `/nearUsers` request |

> Authenticate response for user `UserClotHelps`
````json
{
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZGVudGlmaWVyIjoiNWU4MzlkZDk4OTEzOGMxMDBhYzJlNjBhIiwiaWF0IjoxNTg1Njg1OTM4LCJleHAiOjQ3NDE0NDU5Mzh9.LpJvm1kJJ4GHzJ6FM1qLnHRYahgQxAq3wPCo3yfLpQU"
}
````

--------

# Admin

## Admin Endpoints

Endpoints for admin management.

## Publish config file

Creates or updates a config file (available under the path {URI}/public/..). <br>
**POST** Request

> /admin/configFile

@POST <br>
`/messages`

### Entry parameters

| Name  |  Mandatory  | Type |   Description  |
|:-:|:-:|:-:|:--|
| accessToken  | Yes  | String | Unique identifier to perform the admin action  |
| name  | Yes  | String | Name of the config file (without extensions or "." or "/")  |
| data  | Yes  | Object | The value of the config file  |

### Response parameters

*None*

Invocation example: <br>

@POST <br>
> https://{{url}}/api/v1/admin/configFile

````json
{
    "accessToken" : "SECRET_ASK_THE_ADMIN",
	"name" : "test" ,
	"data" : {
		"a" : 1,
		"b": [1,2,3,4,5],
		"c": {
			"test": 0
		}
	}
}
````

Response example:
@200
````json
{}
````

> In this example a file named "test" will be available under the route `https://{{url}}/public/test.json` with the contents of data returned:

````json
{
    "a": 1,
    "b": [1,2,3,4,5],
    "c": {
        "test": 0
    },
    "_updateTS": "2021-03-31T10:45:53.277Z"
}
````