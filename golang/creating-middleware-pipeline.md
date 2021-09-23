# Creating Middleware Pipeline

I learned how to accomplish a middleware pipelining where a middleware calls
the next middleware until at the end a handler suppose to do the final work.

The handle trick is to create a function that mutates the same scoped variable
so the next time the loop is called, it has the following function

```go
type Request interface{}
type Response interface{}
type RequestHandler func(ctx context.Context, request Request) (Response, error)

// Notice that the middleware is high-order function that creates handlers
type Middleware func(handler RequestHandler) RequestHandler

type router struct {
  handler RequestHandler
  middlewares     []Middleware
}

func (m *router) Dispatch(ctx context.Context, request Request) (Response, error) {}
  // Other stuff ...

  // We use this function instead of calling `runPipeline`, just avoiding
  // some variables declaration here for the sake of
  return m.runPipeline(m.handler, ctx, request, metadata)
}

func (m *router) runPipeline(handler RequestHandler, ctx context.Context, request Request) (Response, error) {
  for _, middleware := range m.middlewares {
    // Notice that we are changing the local variable value
    handler = middleware(handler)
  }
  return handler(ctx, request)
}
```
