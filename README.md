# Wisper::Attributes [WIP]

Attributes are declared as usual, any changes result in an event being
broadcast with details of the changes.

An example use case would be to audit all changes to instances of particular
types, might be useful as a poor persons profiler.

## Installation

```ruby
gem 'wisper-attributes'
```

## Usage

**Starting with a regular class**

```ruby
class CoffeeMaker
  include Wisper::Attributes

  attr_accessor :name
end
```

**An example listener which will record changes in memory**

```ruby
class Auditor
  include Singleton

  attr_accessor :changes

  def initialize
    @changes ||= []
  end

  def on_changed(subject, attribute, changes)
    changes.push(subject_id: subject.id,
                 subject_class: subject.class.to_s,
                 attribute: attribute,
                 from: changes[:from],
                 to: changes[:to])
  end

  def self.changes
    instance.changes
  end
end
```

**For demo purposes we globally subscribe the listener**

```ruby
Wisper.add_listener(Auditor.new)
```

**Make some changes**

```ruby
coffee_maker = CoffeeMaker.new
coffee_maker.name = ‘Happy Days’
```

**And see that the changes that have been recorded**

```ruby
Auditor.changes # => [..]
```

## Contributing

Please submit a Pull Request with specs.
