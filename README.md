sinatra-router
==============

A tiny vendorable router that makes it easy to try routes from a number of different modular Sinatra applications.

In your `Gemfile`:

``` ruby
gem 'sinatra-router'
```

Now as part of a builder or rackup (i.e. `config.ru`):

``` ruby
run Sinatra::Router do
  route API::Apps
  route API::Users
end
```

Or mount it as middleware:

``` ruby
use Sinatra::Router do
  route API::Apps
  route API::Users
end
run Sinatra::Application
```

## Conditional Routing

Add routing conditions with arguments or blocks:

``` ruby
run Sinatra::Router do
  with_conditions(lambda { |e| e["HTTP_X_VERSION"] == "2" }) do
    route API::Apps
    route API::Users
  end

  route API::Users, lambda { |e| e["HTTP_X_VERSION"] == "1" }
end
```

Or extend the class to create your own concise DSL:

``` ruby
module API
  class Router < Sinatra::Router
    version(version, &block)
      condition = lambda { |e| version == e["HTTP_X_VERSION"] }
      if block
        with_conditions(condition, &block)
      else
        condition
      end
    end
  end
end

# config.ru
run API::Router do
  version 2 do
    route API::Apps
    route API::Users
  end

  route API::Users, version(1)
end
```

## Development

``` bash
ruby test/sinatra/router_test.rb
```
