# SOLID

## The Single Responsibility Principle
* Class should have only one responsibility.
* All his methods should be used to perform this responsibility.
* Complex classes should be decomposed to simple classes. Each simple class should be responsible for part of all behavior.

Bad:
```ruby
class Report
  def initialize(data)
    @data = data
  end
  
  def generate_report
     # Some report generation logic
  end

  def print
     JSONFormatter.new.format(generate_report)
  end
end
```

The Report class is responsible for generation and printing. It has two responsibilities, and according to the principle it should only have one. Let’s extract one:

Good:
```ruby
class ReportGenerator
  def initialize(data)
    @data = data
  end
  
  def generate_report
     # Some report generation logic
  end
end

class ReportPrinter
  def initialize(report)
    @report = report
  end
  
  def print
    JSONFormatter.new.format(@report)
  end
end
```

## The Open Closed Principle
Each class should be closed for changes, but be opened for modifications.

Bad:
```ruby
class ReportPrinter
  def initialize(report)
    @report = report
  end
  
  def print
     JSONFormatter.new.format(@report)
  end
end
```

This code violates OCP, because if we want to change the format the report gets printed, you need to change the code of the class. Let’s change it then.

Good:
```ruby
class Report
  def print(formatter: JSONFormatter.new)
     formatter.format(@report)
  end
end
```

Thus I have extended the functionality without modifying the code. In this example, I have used a technique called Dependency Injection, but many others could apply.

## The Liskov Substitution Principle
Objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program.
This principle applies only to inheritance, so let’s look at an example of inheritance that breaks it:

Bad:
```ruby
class Animal
  def walk
     do_some_walkin
  end
end

class Cat < Animal
  def run
    run_like_a_cat
  end
end
```

So, they must have the same interface. Since Ruby does not have abstract methods, we can do it like so:

Good:
```ruby
class Animal
  def walk
     do_some_walkin
  end

  def run
    raise NotImplementedError
  end
end

class Cat < Animal
  def run
    run_like_a_cat
  end
end
```

## The Interface Segregation Principle
* Clients should not be forced to depend upon interfaces that they do not use. 
* Many client-specific interfaces are better than one general-purpose interface.

Bad:
```ruby
class Car
  def open
  end

  def start_engine
  end

   def change_engine
   end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car.change_engine
  end
end
```

Good:
```ruby
class Car
  def open
  end

  def start_engine
  end
end

class CarInternals
   def change_engine
   end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car_internals.change_engine
  end
end
```

## The Dependency Inversion Principle
Abstractions should not depend upon details. Details should depend upon abstractions.

When following this principle, the conventional dependency relationships established from high-level, policy-setting modules to low-level, dependency modules are inverted (i.e. reversed), thus rendering high-level modules independent of the low-level module implementation details.

Bad:
```ruby
class ReportPrinter
  def initialize(report)
    @report = report
  end

  def print_json
     JSONFormatter.new.format(@report)
  end
  
   def print_xml
     XMLFormatter.new.format(@report)
  end
end

class JSONFormatter
  def format(body)
     # some formatting logic
  end
end

class XMLFormatter
  def format(body)
     # some formatting logic
  end
end
```

Now there is a formatter class, but I’ve hardcoded it on the Report class, thus creating a dependency from the Report to the JSONFormatter. Since the Report is a more abstract (high-level) concept than the JSONFormatter, we’re effectively breaking the DIP.

We can solve it the exact same way we solved it on the OCP problem, with dependency injection:

Good:
```ruby
class ReportPrinter
  def initialize(report)
    @report = report
  end

  def print(formatter: JSONFormatter.new)
     formatter.format body
  end
end
```
