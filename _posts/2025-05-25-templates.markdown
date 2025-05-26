---
layout: post
title: "Streamlining the creation of new Rails applications with templates"
date: 2025-05-25
lang: en
---
In one of the clients I’ve worked on, they had several projects. In the process of extracting parts of the legacy project, they had a document with the gems that would be used according to the type of project, whether it was an API, if it had authentication, if it would call other APIs, or would have background processing. Every new project had a separate Dockerfile, GitHub actions configuration, Rubocop configuration, Brakeman, issue templates, etc.

It was laborious to create a new Rails app every time, set it up, and check if everything was okay. It took a lot of time for something that was, in the end, almost a copy-and-paste.

I got tired of this repetitive work and decided to use a Rails feature I felt it was not used enough: templates.

A template is a Ruby file with a DLS that you pass as an argument when you run Rails new. In this file, you can already specify which gems the project will have in addition to the Rails defaults, routes, controllers, and everything else.

Let's take an example: let's say that every project in the company uses RSpec for testing, Airbrake for error monitoring, Rubocop,  adds Devise if desired, creates files for the CI, runs generators after the gems are installed, and adds default locale and settings in the environment files. Instead of every new project doing all this manually, which can take hours, you can create a template.

To explain, let's break it down into parts.

## Add gems:

RSpec would be in the Gemfile's tests group, Robocop in development, and Airbrake in no specific group.

You can have a file like this.

```ruby
gem 'airbrake'

gem_group :development do
  gem 'rubocop', '~> 1.30', require: false
  gem 'rubocop-rspec', '~> 2.11', require: false
  gem 'rubocop-rails', require: false
end

gem_group :development, :test do
  gem "rspec-rails"
end

add_devise = yes?("Add device?")

if add_devise
  gem 'devise'
end
```

The `yes?` method will prompt a message during the `rails new` flow asking the developer to tell you whether or not they want to add the gem, it accepts `y` or `yes` as truthy answer. In this example, the prompt result is added to a variable because we will use it later for other developer configurations;

## Set default locale

To add a default locale, there is the `application` method

```ruby
application 'config.i18n.default_locale = :"en-US"'
```

The above example adds the default locale to the `config/application.rb` file.

```ruby
if add_devise
  environment "config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }", env: "development"
end
```

This example adds the action mailer config only in the development environment if the developer chose to add the devise gem.

## Configuration files

```ruby
empty_directory ".github/workflows"

# need to call app_name outside the `inside` block, or the value for project_name will be the workflows directory name
project_name = app_name

inside(".github/workflows") do
	file "github-actions.yml", URI.open("https://domain.com/github-actions.yml").read
  gsub_file 'github-actions.yml', /<project>/, project_name
end
```

Above we created the github actions workflow directory, downloaded the ci configuration file and replaced placeholders with values specific to our project. Notice the `project_name = app_name` line, if you call the `app_name` method outside the `inside` method, the `project_name` will be memoized and, will not change when called inside the block, but in this case I decided to assign into a variable just to make more readable than just calling the method alone; you can see the `app_name` definition [here](https://github.com/rails/rails/blob/v8.0.2/railties/lib/rails/generators/app_name.rb#L9-L11).

```ruby
after_bundle do
  generate("rspec:install")
  generate(:airbrake)
  generate("devise:install") if add_devise

  git :init
  git add: "."
  git commit: %Q{ -m 'Initial commit' }
end
```

the `after_bundle` method executes the block after executing `bundle install`; with the file done, it's time to create a new project using the template.

## Create project using the template

From then on, each new Rails project you have just needs to pass the file as a parameter to the `-m` argument;

`rails new company_project -m ~/path_of/template.rb`

or if the template is in a public URL you can pass the URL as a parameter `rails new company_project -m https://mydomain.com/template.rb`

the result will be something like the snippet below, notice the line `apply /Users/alessandro/projects/template.rb` showing that the script will use the template, also notice the line `Add device? y` asking is should add the devise

```bash
      ...
       apply  /Users/alessandro/projects/template.rb
     gemfile    airbrake
     gemfile    group :development
        gsub    Gemfile
     gemfile    rubocop (~> 1.30)
     gemfile    rubocop-rspec (~> 2.11)
     gemfile    rubocop-rails
        gsub    Gemfile
     gemfile    group :development, :test
        gsub    Gemfile
     gemfile    rspec-rails
        gsub    Gemfile
  Add device? y
     gemfile    devise
       exist    .github/workflows
      create    .github/workflows/github-actions.yml
        gsub    .github/workflows/github-actions.yml
         run  bundle install --quiet
         ...
       rails  generate rspec:install
      create  .rspec
      create  spec
      create  spec/spec_helper.rb
      create  spec/rails_helper.rb
    generate  airbrake
       rails  generate airbrake
      create  config/initializers/airbrake.rb
    generate  devise:install
       rails  generate devise:install
      create  config/initializers/devise.rb
         run  git init from "."
         run  git add . from "."
         run  git commit  -m 'Initial commit'  from "."
[main (root-commit) 18f979b] Initial commit

```

## Apply the template into already existing apps

You can also apply the template to existing projects running:

```bash
bin/rails app:template LOCATION=~/template.rb
```

Thing to keep in mind when applying a template into a existing project, you might end having duplicated, gems, routes, configs, depending on what you added into your template file, just be sure to handle this if needed.


If you want, you can dig into the Thor and Rais docs here:

- <https://www.rubydoc.info/gems/thor/Thor/Shell/Basic>
- <https://www.rubydoc.info/gems/thor/Thor/Actions>
- <https://guides.rubyonrails.org/rails_application_templates.html>
- <https://api.rubyonrails.org/v8.0.2/classes/Rails/Generators/Actions.html>
- <https://api.rubyonrails.org/v8.0.2/classes/Rails/Generators/AppGenerator.html>

This is a gist that I used in the past to apply a template in a project: [rails_template.rb](https://gist.github.com/adbatista/6189355b46f78fca34b98d5ae1fa58f1).

I hope that this helps you to setup and standardize yours apps faster 😃.
