# Switching on the type of error

I created a custom error struct, and I wanted to make it compatible with
`errors.Is`.

```go
package main

import (
  "fmt"
)

type MyError struct {
  Source string
  Type string
}

func (e StaticError) Error() string {
  return fmt.Sprintf("type(%s) from source(%s)", e.Type, e.Source)
}
```

Since `errors.Is` function must take parameter of `err` as `error`
interface, we can simple do `err.(type)` because the value will be always
`error`.

```go
package main

// This will not work as intended
func (e MyError) Is(err error) bool {
  switch err := err.(type) {
  case MyError: // err.(type) is always `error` this will never match
   return e.Source == err.Source && e.Type == err.Type
  default:
   return false
  }
}
```

The trick is to cast it to an interface first, and then ask for the type.

```go
package main

// This will not work as intended
func (e MyError) Is(err error) bool {
  // Cast to interface{} and then ask for the type
  switch err := err.(interface{}).(type) {
  case MyError:
   return e.Source == err.Source && e.Type == err.Type
  default:
   return false
  }
}
```

I am fairly new to Go, so I am not sure if people prefer to use `reflect` package
using `reflect.TypeOf(target)`, just tried to avoid such package I honestly don't
know the differences, just stick to strong-typing ways to deal with
strong-typing from my perspective.
