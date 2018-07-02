# SOLID

## The Single Responsibility Principle
1. Class should have only one responsibility.
2. All his methods should be used to perform this responsibility.
3. Complex classes should be decomposed to simple classes. Each simple class should be responsible for part of all behavior.

Bad:
```ruby
class Auth
  def authenticate(email, password)
    if matches?(email, password)
     do_some_authentication
    else
      raise NotAllowedError
    end
  end

  private
 
  def matches?(email, password)
    user = User.find(email: email)
    user.encrypted_password == encrypt(password)
  end
end
```

The Auth class is responsible for authenticating the user as well as knowing if the email and password match the ones in the database. It has two responsibilities, and according to the principle it should only have one. Let’s extract one:

Good:
```ruby
class Auth
  def authenticate(email, password)
    if Credentials.new(email, password).matches?
     do_some_authentication
    else
      raise NotAllowedError
    end
  end
end

class Credentials
  def initialize(email, password)
     @email = email
     @password = password
  end

  def matches?
    user = User.find(email: @email)
    user.encrypted_password == encrypt(@password)
  end
end
```

## The Open Closed Principle
Each class should be closed for changes, but be opened for modifications.

Bad:
```ruby
class Report
  def body
     generate_reporty_stuff
  end

  def print
     body.to_json
  end
end
```

This code violates OCP, because if we want to change the format the report gets printed, you need to change the code of the class. Let’s change it then.

Good:
```ruby
class Report
  def body
     generate_reporty_stuff
  end

  def print(formatter: JSONFormatter.new)
     formatter.format body
  end
end
```

Thus I have extended the functionality without modifying the code. In this example, I have used a technique called Dependency Injection, but many others could apply.

For example, we can create 

## The Liskov Substitution Principle
TODO

## The Interface Segregation Principle
TODO

## The Dependency Inversion Principle
TODO
