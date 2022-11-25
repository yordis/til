# Avoid Implementing the Entire Interface

Most of the time when you are working with Service Clients you would like to
mock fewer methods than the entire interface.

For example, you could have a really big interface like this:

```go
package sqsiface

import (
  "github.com/aws/aws-sdk-go/aws"
  "github.com/aws/aws-sdk-go/aws/request"
  "github.com/aws/aws-sdk-go/service/sqs"
)

type SQSAPI interface {
  AddPermission(*sqs.AddPermissionInput) (*sqs.AddPermissionOutput, error)
  AddPermissionWithContext(aws.Context, *sqs.AddPermissionInput, ...request.Option) (*sqs.AddPermissionOutput, error)
  AddPermissionRequest(*sqs.AddPermissionInput) (*request.Request, *sqs.AddPermissionOutput)

  ChangeMessageVisibility(*sqs.ChangeMessageVisibilityInput) (*sqs.ChangeMessageVisibilityOutput, error)
  ChangeMessageVisibilityWithContext(aws.Context, *sqs.ChangeMessageVisibilityInput, ...request.Option) (*sqs.ChangeMessageVisibilityOutput, error)
  ChangeMessageVisibilityRequest(*sqs.ChangeMessageVisibilityInput) (*request.Request, *sqs.ChangeMessageVisibilityOutput)

  // ... and so on
}
```

You want to use the `sqsiface.SQSAPI` interface, but you do not want to
implement the entire interface in testing environment because you only used
one method from the interface.

You can create a `nil` object that implements the interface

```go
var _ SQSAPI = (*sqs.SQS)(nil)
```

and then you can create a mock object "extending" the `nil` object.

```go
package mypackage_test

import sqsiface

// Define a mock struct to be used in your unit tests of myFunc.
type mockSQSClient struct {
    sqsiface.SQSAPI
}
func (m *mockSQSClient) AddPermission(input *sqs.AddPermissionInput) (*sqs.AddPermissionOutput, error) {
// mock response/functionality
}

func TestMyFunc(t *testing.T) {
  // Setup Test
  mockSvc := &mockSQSClient{}
  
  myfunc(mockSvc)
  
  // Verify myFunc's functionality
}
```

## References

- https://github.com/aws/aws-sdk-go/blob/main/service/sqs/sqsiface/interface.go
