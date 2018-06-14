# Messaging Library

[![Go Report Card](https://goreportcard.com/badge/container-mgmt/messaging-library)](https://goreportcard.com/report/github.com/container-mgmt/messaging-library)
[![Build Status](https://travis-ci.org/container-mgmt/messaging-library.svg?branch=master)](https://travis-ci.org/container-mgmt/messaging-library)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

This package provides a Go library that simplifies the implementation of
communication patterns, like request/reply, on top of a messaging broker
that supports queues and topics.

## Building

To build the project clone the repository to your go path and run the
`make` command.

To build the example `messaging-tool` run the `make binaries` command.

## Usage

### Publish a string message to a topic

Example:
[send.go](/cmd/messaging-tool/send.go)

``` go
import (
  ...

  // Import the message broker client interface.
  "github.com/container-mgmt/messaging-library/pkg/client"

  // Import a STOMP client implementation.
  "github.com/container-mgmt/messaging-library/pkg/connections/stomp"
)

...

  // Create a new connection object.
  var c client.Connection

  // Set a new STOMP connction to localhost:1888.
  c, err = stomp.NewConnection(&stomp.ConnectionBuilder{
    BrokerHost:   "lolcalhost",
    BrokerPort:   "1888",
  })
  err = c.Open()
  defer c.Close()

  // Make a message with plain text body "Hello subscriber.".
  m = client.Message{
    ContentType: "text/plain",
    Body:        "Hello subscriber.",
  }

  // Publish our hello message to the "topic name" topic.
  err = c.Publish(m, "topic name")
```

### Subscribe to a topic

Example:
[receive.go](/cmd/messaging-tool/receive.go)


``` go
import (
  ...

  // Import the message broker client interface.
  "github.com/container-mgmt/messaging-library/pkg/client"

  // Import a STOMP client implementation.
  "github.com/container-mgmt/messaging-library/pkg/connections/stomp"
)

...

// we will use this function as a callback function for each new message
// published on specific topic.
func callback(m client.Message, topic string) (err error) {
	glog.Infof(
		"Received message from destination '%s':\n%s",
		topic,
		m.Body,
	)

	return
}

...

  // Create a new connection object.
  var c client.Connection

  // Set a new STOMP connction to localhost:1888.
  c, err = stomp.NewConnection(&stomp.ConnectionBuilder{
    BrokerHost:   "lolcalhost",
    BrokerPort:   "1888",
  })
  err = c.Open()
  defer c.Close()

  ...

  // Subscribe to the topic "topic name", and run callback function for each
  // new message.
  err = c.Subscribe("topic name", callback)
```
