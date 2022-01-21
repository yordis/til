# Get the fully qualified struct name

Sometimes you need to get the fully qualified struct name to do some sort of 
mapping between a struct and a string.

[//]: # (hide)

```go
package name

import (
  "fmt"
  "strings"
)

// FullyQualifiedStructName name returns object name in format [package].[type name].
// It ignores if the value is a pointer or not.
func FullyQualifiedStructName(v interface{}) string {
  s := fmt.Sprintf("%T", v)
  s = strings.TrimLeft(s, "*")

  return s
}
```
