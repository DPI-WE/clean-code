# Crafting Clean Code 🛀

## Introduction to Clean Code Practices
Clean code is the hallmark of a thoughtful developer. It not only demonstrates consideration for your future self but also for others who may work on your code. Drawing inspiration from Robert Martin's principles in "Clean Code," this lesson weaves these timeless guidelines into the fabric of development, ensuring your codebase remains robust, maintainable, and enjoyable to work with.

## The Essence of Clean Code
Clean code is easily understood by anyone on the team. It is characterized by readability, simplicity, and the ability to be enhanced by a developer other than its original author. These attributes are crucial for the sustainability and scalability.

## Core Principles

### Follow Standard Conventions
Embrace conventions for naming, structure, and coding practices. This not only makes your code cleaner but also ensures it aligns with the expectations of your team.

```ruby
# Bad: Non-standard naming and casing in Ruby
class Userinfo < ApplicationRecord
  def fullName
    "#{first_name} #{last_name}"
  end
end

# Good: Conventional naming and casing in Ruby
class User < ApplicationRecord
  def full_name
    "#{first_name} #{last_name}"
  end
end
```

### Keep It Simple
Simplicity is at the heart of clean code. This means choosing the most straightforward approach to solve problems, utilizing built-in methods, and avoiding over-engineering solutions.

```ruby
# Bad: Unnecessarily complex (unless a hard requirement)
def greet(name)
  time = Time.now
  hour = time.hour
  if hour < 12
    "Good morning, #{name}"
  elsif hour < 18
    "Good afternoon, #{name}"
  else
    "Good evening, #{name}"
  end
end

# Good: Simple and Direct
def greet(name)
  "Hello, #{name}"
end
```

### The Boy Scout Rule
Leave your code better than you found it. When working on a project, always look for opportunities to refactor and improve the code you touch, even if it's outside the scope of your current task.

### Seek the Root Cause
When bugs arise, don't just patch symptoms. Investigate and address the underlying issues, ensuring your applications are robust and reliable.

```ruby
# Bad: Patching symptoms
def process_data(data)
  return unless data
  data.strip!
  data.capitalize!
end

# Good: Addressing the root cause
def process_data(data)
  raise ArgumentError, "Data can't be nil" if data.nil?
  data.strip.capitalize
end
```

## Design Guidelines

### Promote Polymorphism
Embrace polymorphism in relationships and routing, allowing for cleaner, more maintainable code. Use polymorphism over conditional logic to make your models and controllers more extensible.

```ruby
class Animal
  def speak
    raise NotImplementedError, "This method should be overridden by subclass"
  end
end

class Dog < Animal
  def speak
    "Woof!"
  end
end

class Cat < Animal
  def speak
    "Meow!"
  end
end

# Good: Using polymorphism to call the appropriate method
[Dog.new, Cat.new].each { |animal| puts animal.speak }
```

### Dependency Injection
Use dependency injection, especially in testing, to decouple components and make your code more modular and testable.

```ruby
# Without Dependency Injection
class UserNotifier
  def notify(user)
    Mailer.send_email(user.email, 'Notification')
  end
end

# With Dependency Injection
class UserNotifier
  def initialize(mailer)
    @mailer = mailer
  end

  def notify(user)
    @mailer.send_email(user.email, 'Notification')
  end
end

notifier = UserNotifier.new(Mailer)
```

### Law of Demeter
Adhere to the Law of Demeter by ensuring that objects interact with their immediate associations, reducing coupling and increasing cohesion within your applications.

```ruby
# Bad: Violating Law of Demeter
def display_user_country(user)
  country = user.address.country
  puts country.name
end

# Good: Adhering to Law of Demeter
def display_user_country(user)
  country = user.country_name  # delegate method in User model
  puts country
end
```

## Understandability and Names

### Consistency and Clarity
Be consistent in how you structure and write your code. Use clear, descriptive names for classes, methods, and variables. Avoid cryptic abbreviations and ensure names accurately reflect the entity's purpose or function.

```ruby
# Bad: Inconsistent and Unclear Naming
class OrderProc
  def prc_ord(ord)
    chk_ord(ord)
    chrg_pymt(ord)
    snd_conf(ord)
  end
end

# Good: Consistent and Clear Naming
class OrderProcessor
  def process_order(order)
    validate_order_details(order)
    charge_payment(order)
    send_order_confirmation(order)
  end
end
```

### Encapsulate Boundary Conditions
Applications often interact with external services and APIs. Encapsulate these interactions within dedicated objects or services to isolate complexity and enhance readability.

```ruby
# External API client class for weather data
# Encapsulates all the details of communicating with the weather API
class WeatherApiClient
  require 'net/http'
  require 'json'

  API_URL = "https://api.openweathermap.org/data/2.5/weather"
  API_KEY = "your_api_key_here"

  # Fetches weather data for a specified city
  def self.get_weather_by_city(city_name)
    uri = URI("#{API_URL}?q=#{city_name}&appid=#{API_KEY}")
    response = Net::HTTP.get(uri)
    JSON.parse(response)
  end
end

# Weather service that uses the WeatherApiClient to fetch and process weather data
# Separate concerns between data fetching (ApiClient) and data processing (Service)
class WeatherService
  # Returns a simplified weather report for a given city
  def get_weather_report(city_name)
    raw_data = WeatherApiClient.get_weather_by_city(city_name)
    format_weather(raw_data)
  end

  private

  # Formats raw weather data into a user-friendly report
  def format_weather(data)
    temperature = data['main']['temp']
    description = data['weather'].first['description']

    "The current weather is #{temperature} degrees with #{description}."
  end
end

weather_service = WeatherService.new
puts weather_service.get_weather_report("Chicago")
```

### Descriptive Names Over Comments
Strive to make your code self-explanatory. Choose method and variable names that convey their purpose without needing additional comments. When comments are necessary, use them to explain the "why" behind a decision, not the "what" or "how."

```ruby
# Bad: Non-descriptive naming with unnecessary comments
def calc_sal(hr_rate, hrs_per_week) # Calculates annual salary
  hr_rate * hrs_per_week * 52
end

# Good: Descriptive naming
def calculate_annual_salary(hourly_rate, hours_per_week)
  hourly_rate * hours_per_week * 52
end
```

## Code Structure and Functions

### Small, Dedicated Methods
Follow the single responsibility principle for methods. Each method should do one thing and do it well. This practice leads to more reusable and easier-to-test code.

```ruby
# Bad: Multi-purpose method
def manage_contact(name, phone)
  raise "Invalid details" if name.empty? || phone.empty?
  contact_file = File.open("contacts.txt", "a")
  contact_file.puts("#{name}, #{phone}")
  contact_file.close
  puts "Contact #{name} added!"
end

# Good: Small, focused methods
def add_contact(name, phone)
  validate_contact(name, phone)
  save_contact(name, phone)
  notify_user(name)
end

def validate_contact(name, phone)
  raise "Invalid contact details" if name.empty? || phone.empty?
end
```

### Variables and Functions Proximity
Declare variables close to where they are used. Organize methods so related functionality is vertically close, enhancing the readability and navigability of your code.

```ruby
# Bad: Declaring all variables upfront
def apply_discount(price, discount_rate)
  final_price = nil
  calculated_discount = nil

  calculated_discount = price * discount_rate
  final_price = price - calculated_discount
  puts "Discounted price: #{final_price}"
end

# Good: Variables declared near usage
def calculate_discount(price, discount_rate)
  final_price = price - (price * discount_rate)
  puts "The final price is #{final_price}"
end
```

## Testing
Tests are the bedrock of maintainable code. Ensure your tests are fast, independent, and repeatable. Embrace the one assert per test rule for clarity and simplicity.

## Handling Code Smells
Rigidity, fragility, and immobility are symptoms of deeper problems within a codebase. Regularly refactor your applications to address these issues, focusing on simplifying complex areas, removing duplication, and improving readability.

## Quiz

- What is a primary characteristic of clean code?
- It relies heavily on comments for clarity.
  - Not quite. While comments can be helpful, clean code should be self-explanatory and not dependent on comments for understanding.
- It includes complex logic to show off developer skills.
  - Incorrect. Clean code should prioritize simplicity over complexity, making it easier to read and maintain.
- It is simple and easy to understand by anyone on the team.
  - That's right! Clean code should be clean, readable, and understandable without requiring deep analysis or explanations.
- It frequently uses advanced features
  - Incorrect. Using advanced features indiscriminately does not align with clean code principles, which emphasize simplicity and clarity.
{:.choose_best #clean_code_characteristic title="Characteristics of Clean Code" points="1" answer="3" }

- What should you do when you encounter messy code that is not part of your current task?
- Ignore it completely and focus only on your assigned task.
  - Not quite. While focus is important, clean code practices encourage making minor improvements.
- Make improvements if they are related to your assigned task.
  - Correct! The Boy Scout Rule encourages you to leave the code better than you found it, promoting ongoing improvement.
- Overhaul the entire module to apply clean code principles.
  - This is not recommended as it might introduce risks and delays in your current project scope.
- Document the issues for future reference but do not make changes.
  - Practically sound, but it’s better to make minor improvements right away if possible.
{:.choose_best #boy_scout_rule title="Application of the Boy Scout Rule" points="1" answer="2" }

- Why is dependency injection recommended in clean code practices?
- To increase the complexity and robustness of the application.
  - Incorrect. Dependency injection actually helps reduce complexity by decoupling components.
- To ensure tight coupling of components for better performance.
  - Not quite. Dependency injection aims to reduce coupling, making the code more modular and easier to maintain.
- To decouple components, making the code more modular and testable.
  - Exactly! Dependency injection facilitates easier maintenance and testing by reducing dependencies between components.
- To utilize more memory resources for faster code execution.
  - This is incorrect. Dependency injection isn't about using more resources but about creating a cleaner, more maintainable codebase.
{:.choose_best #dependency_injection title="Benefits of Dependency Injection" points="1" answer="3" }

- What does the Law of Demeter promote?
- Frequent communication between all objects and classes.
  - Incorrect. The Law of Demeter actually encourages reducing unnecessary interactions between objects.
- Objects interacting only with their direct associations.
  - Correct! This practice minimizes dependencies and interactions, leading to a more maintainable and comprehensible codebase.
- All classes having only single and specific functionality.
  - This is partly correct, but it's more related to the Single Responsibility Principle rather than the Law of Demeter.
- Methods and properties accessible from any part of the application.
  - Not advisable. The Law of Demeter teaches us to limit the exposure and interaction of components.
{:.choose_best #law_of_demeter title="Understanding the Law of Demeter" points="1" answer="2" }

- In the context of clean code, which practice is most aligned with ensuring maintainability and readability?
- Creating large, comprehensive methods that perform multiple tasks.
  - Incorrect. Clean code favors small, focused methods that perform a single task.
- Methods focused on performing one task.
  - Exactly right! This keeps methods cohesive and focused, vastly improving readability and maintainability.
- Keeping unrelated functions in the same file for quick access.
  - This can lead to confusing and unmanageable code, against clean code principles.
- Combining both business logic and helper methods in the same class.
  - Incorrect. It's better to separate concerns to enhance clarity and ease of testing.
{:.choose_best #abstraction_principle title="Single Level of Abstraction" points="1" answer="2" }

## Conclusion
Incorporating Robert Martin's clean code principles into your development practices elevates the quality of your work and fosters a culture of excellence within your team. By adhering to these guidelines, your applications will not only be a pleasure to work with but also more robust, scalable, and maintainable.

Remember, writing clean code is not an endpoint but a journey. Continuously learn, adapt, and improve your coding practices, and your applications will stand the test of time. Embark on your journey towards clean code mastery, creating applications that are not only functional but also a joy to develop and maintain.

## Resources
- [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [Summary of 'Clean code' by Robert C. Martin](https://gist.github.com/wojteklu/73c6914cc446146b8b533c0988cf8d29)
