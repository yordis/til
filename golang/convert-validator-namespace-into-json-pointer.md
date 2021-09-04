# Convert Validator Namespace into JSON-Pointer

I was trying to create some structured error following the
[adrs#6860374633](https://github.com/straw-hat-team/adr/tree/master/adrs/6860374633.

And by default [Gin](https://github.com/gin-gonic/gin) validator configuration
doesn't use the struct JSON tag for the namespace and I need to use the JSON
tag name instead of struct field name in order to create a [JSON-Pointer](https://datatracker.ietf.org/doc/html/rfc6901).

```go
package main

import (
  "fmt"
  "github.com/gin-gonic/gin/binding"
  "github.com/go-playground/validator/v10"
  "reflect"
  "strings"
)

// Used the example from the validator package
func JSONTagName(fld reflect.StructField) string {
  name := strings.SplitN(fld.Tag.Get("json"), ",", 2)[0]
  if name == "-" {
    return ""
  }
  return name
}

// Convert the namespace into a JSON-Pointer, ignoring the struct name
func NamespaceToJsonPointer(field validator.FieldError) string {
  parts := strings.Split(strings.ToLower(field.Namespace()), ".")
  return fmt.Sprintf("/%s", strings.Join(parts[1:], "/"))
}

func main() {
  // Change the default tag name lookup
  if validate, ok := binding.Validator.Engine().(*validator.Validate); ok {
    validate.RegisterTagNameFunc(JSONTagName)
  }
}
```
