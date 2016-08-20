Configuring Minitest on a blank gem
===================================

Just follow the steps:
---------------------

Create your gem:
```bash
bundle gem <gem_name>
```

Add guard-minitest to the .gemspec:
```ruby
spec.add_development_dependency "guard"
spec.add_development_dependency "guard-minitest"
```

Create a standard guard file:
```bash
bundle exec guard init .
```

Call it:
```bash
bundle exec guard
```
