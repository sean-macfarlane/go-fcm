# go-fcm : FCM Library for Go

[![AUR](https://img.shields.io/aur/license/yaourt.svg?style=flat-square)](https://github.com/NaySoftware/go-fcm/blob/master/LICENSE)

Firebase Cloud Messaging ( FCM ) Library using golang ( Go )

This library uses HTTP/JSON Firebase Cloud Messaging connection server protocol

This is a fork of https://github.com/NaySoftware/go-fcm
Changed so it will work in Google App Engine

###### Features

* Send messages to a topic
* Send messages to a device list
* Message can be a notification or data payload
* Supports condition attribute (fcm only)
* Instace Id Features
	- Get info about app Instance
	- Subscribe app Instance to a topic
	- Batch Subscribe/Unsubscribe to/from a topic
	- Create registration tokens for APNs tokens


## Usage

```
go get github.com/sean-macfarlane/go-fcm
```

## Docs - go-fcm API
```
https://godoc.org/github.com/NaySoftware/go-fcm
```

####  Firebase Cloud Messaging HTTP Protocol Specs
```
https://firebase.google.com/docs/cloud-messaging/http-server-ref
```

#### Firebase Cloud Messaging Developer docs
```
https://firebase.google.com/docs/cloud-messaging/
```

#### (Google) Instance Id Server Reference
```
https://developers.google.com/instance-id/reference/server
```
### Notes




> a note from firebase console

```
Firebase Cloud Messaging tokens have replaced server keys for
sending messages. While you may continue to use them, support
is being deprecated for server keys.
```


###### Firebase Cloud Messaging token ( new token )

serverKey variable will also hold the new FCM token by Firebase Cloud Messaging

Firebase Cloud Messaging token can be found in:

1. Firebase project settings
2. Cloud Messaging
3. then copy the Firebase Cloud Messaging token


###### Server Key

serverKey is the server key by Firebase Cloud Messaging

Server Key can be found in:

1. Firebase project settings
2. Cloud Messaging
3. then copy the server key

[will be deprecated by firabase as mentioned above!]

###### Retry mechanism

Retry should be implemented based on the requirements.
Sending a request will result with a "FcmResponseStatus" struct, which holds
a detailed information based on the Firebase Response, with RetryAfter
(response header) if available - with a failed request.
its recommended to use a backoff time to retry the request - (if RetryAfter
	header is not available).




# Examples

### Send to A topic

```go

package main

import (
	"fmt"
    "github.com/sean-macfarlane/go-fcm"
)

const (
	 serverKey = "YOUR-KEY"
     topic = "/topics/someTopic"
)

func main(ctx appengine.Context) {

	client := urlfetch.Client(ctx)
    c := fcm.NewFcmClient(serverKey, client)  

	data := map[string]string{
		"msg": "Hello World1",
		"sum": "Happy Day",
	}

	c.NewFcmMsgTo(topic, data)

	status, err := c.Send()


	if err == nil {
    status.PrintResults()
	} else {
		fmt.Println(err)
	}

}


```


### Send to a Push Notification to a list of Devices (tokens)

```go

package main

import (
	"fmt"
    "github.com/sean-macfarlane/go-fcm"
)

const (
	 serverKey = "YOUR-KEY"
)

func main(ctx appengine.Context) {

	client := urlfetch.Client(ctx)
    c := fcm.NewFcmClient(serverKey, client)  

    var NP fcm.NotificationPayload 
    NP.Title= "Hello World"
    NP.Body= "Happy Day"

	data := map[string]string{
		"msg": "Hello World",
		"sum": "Happy Day",
	}

  ids := []string{
      "token1",
  }


  xds := []string{
      "token5",
      "token6",
      "token7",
  }

    c.NewFcmRegIdsMsg(ids, data)
    c.AppendDevices(xds)	
    c.SetNotificationPayload(&NP)

	status, err := c.Send()


	if err == nil {
    status.PrintResults()
	} else {
		fmt.Println(err)
	}

}



```
