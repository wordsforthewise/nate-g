# Personal site of Nate G.

# Running locally
To run locally (Ubuntu 16.04 LTS), I had to do:

`sudo apt-get install ruby-all-dev ruby-dev`
https://stackoverflow.com/a/4502672/4549682

`sudo gem install bundle`

added `gem 'pygments.rb'` to the Gemfile
https://github.com/stephencelis/ghi/issues/221

`bundle install` (from the home directory of this repo)

- change url in \_config.yml to http://localhost:8888
- change basurl in \_config.yml to be blank
`bundle exec jekyll serve --watch --port 8888`
https://stackoverflow.com/a/25261518/4549682

## Email signups
Used this page to setup email signups: https://github.com/jamiewilson/form-to-google-sheets
