!SLIDE
title
<h1><span>Herbivore<span>Carnivore</span></h1>
## Dinosaurs, Lasers & Acceptance testing

!SLIDE
# Jeff Kreeftmeijer

!SLIDE
# 80beans

!SLIDE
# Open source
## Navvy, Fuubar, Swinger
### Ruby on Rails, RSpec, Steak

!SLIDE
# jeffkreeftmeijer.com

!SLIDE  black
# Behavior Driven Development

!SLIDE
## &ldquo;An agile software development technique that encourages collaboration between developers, QA and non-technical or business participants in a software project.&rdquo;

!SLIDE black
# Cucumber
## &ldquo;Behavior Driven Development with elegance and joy&rdquo;

!SLIDE
@@@ cucumber
    Feature: Todos
      In order to remember things I still need to do
      As a user
      I want to have a manageable list of todos

      Scenario: Add a new todo
        Given I am on the todos page
        When I follow "Add new todo"
        And I fill in "description" with "Talk at ams.rb"
        And I press "Add"
        Then I should see "Successfully created a new todo"
@@@

!SLIDE
@@@ cucumber
    Feature: Todos
      In order to remember things I still need to do
      As a user
      I want to have a manageable list of todos
@@@

!SLIDE
@@@ cucumber
    Feature: Todos
      In order to [Business value]
      As a [Role]
      I want to [Feature]
@@@

!SLIDE
@@@ cucumber
    Scenario: Add a new todo
      Given I am on the todos page
      When I follow "Add new todo"
      And I fill in "description" with "Talk at ams.rb"
      And I press "Add"
      Then I should see "Successfully created a new todo"
@@@

!SLIDE
# Step deifinitions

!SLIDE
@@@ ruby
    Given /^(?:|I )am on (.+)$/ do |page_name|
      visit path_to(page_name)
    end

    When /^(?:|I )follow "([^"]*)"$/ do |link|
      click_link(link)
    end

    When /^(?:|I )fill in "([^"]*)" with "([^"]*)"$/ do |field, value|
      fill_in(field, :with => value)
    end

    When /^(?:|I )press "([^"]*)"$/ do |button|
      click_button(button)
    end

    Then /^(?:|I )should see "([^"]*)"$/ do |text|
      page.should have_content text
    end
@@@

!SLIDE
# Communication

!SLIDE
# Executable documentation

!SLIDE small
@@@ cucumber
    Feature: --line option

      To run a single example or group, you can use the --line option:

          rspec path/to/example_spec.rb --line 37

      Scenario: standard examples
        Given a file named "example_spec.rb" with:
          """
          require "rspec/expectations"

          describe 9 do

            it "should be > 8" do
              9.should be > 8
            end

            it "should be < 10" do
              9.should be < 10
            end

          end
          """
        When I run "rspec example_spec.rb --line 5 --format doc"
        Then the output should contain "1 example, 0 failures"
        Then the output should contain "should be > 8"
        But the stdout should not contain "should be < 10"
@@@

!SLIDE
# Relish

!SLIDE
# Cucumber is awesome

!SLIDE black
# !Cucumber

!SLIDE
# You don't need to communicate in English

!SLIDE
# Examples are not documentation

!SLIDE
# You don't want to write regular expressions

!SLIDE
# You're using another tool to do stories

!SLIDE black
# Steak
## &ldquo;Because Cucumber is for vegetarians&rdquo;

!SLIDE
# RSpec extension

!SLIDE
# Generators

!SLIDE
@@@ console
    $ rails g rspec:install
          create  .rspec
          create  spec
          create  spec/spec_helper.rb

    $ rails g steak:install
    Defaulting to Capybara...
          create  spec/acceptance/support
          create  spec/acceptance/acceptance_helper.rb
          create  spec/acceptance/support/helpers.rb
          create  spec/acceptance/support/paths.rb
@@@

!SLIDE
@@@ ruby
    # spec/acceptance/acceptance_helper.rb

    require File.expand_path(File.dirname(__FILE__) + "/../spec_helper")
    require "steak"
    require 'capybara/rails'

    module Steak::Capybara
      include Rack::Test::Methods
      include Capybara

      def app
        ::Rails.application
      end
    end

    RSpec.configuration.include Steak::Capybara, :type => :acceptance

    # Put your acceptance spec helpers inside /spec/acceptance/support
    Dir["#{File.dirname(__FILE__)}/support/**/*.rb"].each {|f| require f}
@@@

!SLIDE
@@@ console
    $ rails g steak:spec todos
          exist  spec/acceptance
         create  spec/acceptance/todos_spec.rb
@@@

!SLIDE
@@@ ruby
    # spec/acceptance/todos_spec.rb

    require File.expand_path(File.dirname(__FILE__) + '/acceptance_helper')

    feature "Todos", %q{
      In order to ...
      As a ...
      I want to ...
    } do

      scenario "Scenario name" do
        true.should == true
      end
    end
@@@

!SLIDE
# Aliases
## feature &rarr; describe
## scenario &rarr; example/it
## background &rarr; before


!SLIDE
# Writing our first Steak spec

!SLIDE
@@@ ruby
    feature "Todos", %q{
      In order to [Business value]
      As a [Role]
      I want to [Feature]
    } do
@@@

!SLIDE
@@@ ruby
    feature "Todos", %q{
      In order not to forget things I still need to do
      As a user
      I want to have a managable list of todos
    } do
@@@

!SLIDE
@@@ ruby
    scenario "Add a new todo" do
      visit todos_page
      click_link 'Add new todo'

      fill_in 'description', :with => 'Talk at ams.rb'
      click_button 'Add'

      page.should have_content 'Successfully created a new todo'
    end
@@@

!SLIDE
<pre>$ bundle exec rspec spec</pre>
<br/>
<pre>
  1) Todos In order not to forget things I still need to do
  As a user
  I want to have a manageable list of todos Add a new todo
</pre>
<pre style="color: red;">     Failure/Error: visit todos_page
     NameError:
     undefined local variable or method `todos_page' for #<RSpec::Core::ExampleGroup::Nested_1:0x103a53d28></pre>
<pre style="color: gray;">     # ./spec/acceptance/todos_spec.rb:10</pre>
<br/>
<pre style="color:red;">  1/1:         100% |==========================================| Time: 00:00:00</pre>
<br/>
<pre>Finished in 0.076 seconds</pre>
<pre style="color:red;">1 example, 1 failure</pre>

!SLIDE
<div style="opacity: 0.5;">
@@@ ruby
scenario "Add a new todo" do
@@@
</div>
@@@ ruby
      visit todos_page
@@@
<div style="opacity: 0.5;">
@@@ ruby
  click_link 'Add new todo'

  fill_in 'description', :with => 'Talk at ams.rb'
  click_button 'Add'

  page.should have_content 'Successfully created a new todo'
end
@@@
</div>

!SLIDE
@@@ ruby
    # spec/acceptance/support/paths.rb

    module NavigationHelpers
      # Put helper methods related to the paths in your application here.

      def homepage
        "/"
      end
    end

    RSpec.configuration.include NavigationHelpers, :type => :acceptance
@@@

!SLIDE
<div style="opacity: 0.5;">
@@@ ruby
# spec/acceptance/support/paths.rb

module NavigationHelpers
  # Put helper methods related to the paths in your application here.

  def homepage
    "/"
  end
@@@
</div>
<br/>
@@@ ruby
      def todos_page
        "/todos"
      end
    end
@@@
<br/>
<div style="opacity: 0.5;">
@@@ ruby
RSpec.configuration.include NavigationHelpers, :type => :acceptance
@@@


!SLIDE
<pre>$ bundle exec rspec spec</pre>
<br/>
<pre>
  1) Todos In order not to forget things I still need to do
  As a user
  I want to have a manageable list of todos Add a new todo
</pre>
<pre style="color: red;">     Failure/Error: click_link 'Add new todo'
     Capybara::ElementNotFound:
     no link with title, id or text 'Add new todo' found</pre>
<pre style="color: gray;">     # ./spec/acceptance/todos_spec.rb:11</pre>
<br/>
<pre style="color:red;">  1/1:         100% |==========================================| Time: 00:00:00</pre>
<br/>
<pre>Finished in 0.28307 seconds</pre>
<pre style="color:red;">1 example, 1 failure</pre>

!SLIDE
<pre>$ bundle exec rspec spec</pre>
<br/>
<pre>
  1) Todos In order not to forget things I still need to do
  As a user
  I want to have a managable list of todos Add a new todo
</pre>
<pre style="color: red;">     Failure/Error: fill_in 'description', :with => 'Talk at ams.rb'
     cannot fill in, no text field, text area or password field with id, name, or label 'description' found</pre>
<pre style="color: gray;">     # ./spec/acceptance/todos_spec.rb:12</pre>
<br/>
<pre style="color:red;">  1/1:         100% |==========================================| Time: 00:00:00</pre>
<br/>
<pre>Finished in 0.29439 seconds</pre>
<pre style="color:red;">1 example, 1 failure</pre>
@@@

!SLIDE
<pre>$ bundle exec rspec spec</pre>
<br/>
<pre style="color:limegreen;">  1/1:         100% |==========================================| Time: 00:00:00</pre>
<br/>
<pre>Finished in 0.18307 seconds</pre>
<pre style="color:limegreen;">1 example, 0 failures</pre>

!SLIDE
# Helpers

!SLIDE
@@@ ruby
    # spec/acceptance/support/helpers.rb

    module HelperMethods

      # Put helper methods you need to be available in all tests here.

    end

    RSpec.configuration.include HelperMethods, :type => :acceptance
@@@

!SLIDE
<div style="opacity: 0.5;">
@@@ ruby
# spec/acceptance/support/helpers.rb

module HelperMethods
@@@
</div>
<br/>
@@@ ruby
      def create_new_todo(description)
        visit todos_page
        click_link 'Add new todo'

        fill_in 'description', :with => description
        click_button 'Add'
      end
@@@
<br/>
<div style="opacity: 0.5;">
@@@ ruby

end

RSpec.configuration.include HelperMethods, :type => :acceptance
@@@

!SLIDE
<div style="opacity: 0.5;">
@@@ ruby
scenario "Add a new todo" do
@@@
<br/>
</div>
@@@ ruby
      create_new_todo('Talk at ams.rb')
@@@
<div style="opacity: 0.5;">
@@@ ruby
  page.should have_content 'Successfully created a new todo'

end
@@@

!SLIDE
# Steak

!SLIDE
# It's just RSpec
## with some generators and aliases

!SLIDE small
@@@ ruby
    require File.dirname(__FILE__) + '/rails/railtie' if defined?(Rails)

    module Steak
      module AcceptanceExampleGroup
        def self.included(base)
          base.instance_eval do
            alias scenario example
            alias background before

            if defined?(RSpec::Rails)
              include RSpec::Rails::RequestExampleGroup
              include Rack::Test::Methods
              metadata[:type] = :acceptance
            end
          end
        end
      end

      module DSL
        def feature(*args, &block)
          args << {} unless args.last.is_a?(Hash)
          args.last.update :type => :acceptance, :steak => true, :caller => caller
          describe(*args, &block)
        end
      end
    end

    extend Steak::DSL

    RSpec.configuration.include Steak::AcceptanceExampleGroup, :type => :acceptance
@@@

!SLIDE
# 25 LoC

!SLIDE wycats tweet

!SLIDE
# Acceptance testing is not rocket science