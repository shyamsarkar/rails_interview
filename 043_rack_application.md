# Rack Application and Rack-Based Architecture

## What is Rack?

Rack is a minimal Ruby web interface, or specification, between a web server
and a Ruby web application or framework.

Examples of web servers include Puma, Unicorn, and Passenger. Examples of
Rack-compatible frameworks include Rails, Sinatra, and Hanami.

Rack standardizes two things:

- **Request input:** a Ruby `env` hash containing HTTP request information.
- **Response output:** a three-element array: `[status, headers, body]`.

This contract decouples web servers from frameworks. A server only needs to
know how to build `env` and how to send the returned response; it does not
need to know how Rails processes the request internally.

## What is a Rack application?

A Rack application is any Ruby object that responds to `call` and accepts the
Rack environment hash:

```ruby
class HelloApp
   def call(env)
      [200, { "Content-Type" => "text/plain" }, ["Hello from Rack"]]
   end
end
```

The return value must have this shape:

```ruby
[status, headers, body]
```

For example:

- `status` is an HTTP status code such as `200` or `404`.
- `headers` is a hash with string keys and values.
- `body` responds to `each`, commonly an array of strings.

Rails is also a Rack application. The Rails entry point can be invoked using:

```ruby
Rails.application.call(env)
```

## What is Rack-based architecture?

Rack-based architecture composes an application from a stack of middleware.
Each middleware usually:

1. Receives the request.
2. Performs one focused responsibility, such as logging, sessions, or
    authentication.
3. Passes the request to the next application in the stack.
4. Receives the response and can inspect or modify it before returning it.

The flow is therefore nested: the request moves down the stack, and the
response unwinds back through the same middleware.

```text
Web server
      |
      v
Rack middleware 1
      |
      v
Rack middleware 2
      |
      v
Rails application
      |
      v
Router -> Controller -> View
      |
      v
Response unwinds through middleware 2 and middleware 1
      |
      v
Web server sends the response
```

A simplified middleware looks like this:

```ruby
class RequestLogger
   def initialize(app)
      @app = app
   end

   def call(env)
      puts "Request: #{env["REQUEST_METHOD"]} #{env["PATH_INFO"]}"
      status, headers, body = @app.call(env)
      puts "Response: #{status}"
      [status, headers, body]
   end
end
```

## Interview answer

> Rack is a minimal Ruby web interface that standardizes how web servers pass
> requests to Ruby applications and how those applications return responses.
> A Rack application responds to `call(env)` and returns `[status, headers,
> body]`. Rails is a Rack application, and its middleware stack lets each
> component handle one focused responsibility while remaining composable.