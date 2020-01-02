source "https://rubygems.org"
ruby "~> 2.4.4"

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#

# This is the default theme for new Jekyll sites. 
gem "minima", "~> 2.0"

# The following gem has to be used whenever we build the website to
# be hosted on Github. To upgrade, run `bundle update github-pages`.
gem "github-pages", group: :jekyll_plugins
gem "jekyll-remote-theme"

# If you have any plugins, put them here!
group :jekyll_plugins do
   gem "jekyll-feed", "~> 0.6"
   gem "jekyll-gist"
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem "tzinfo-data", platforms: [:mingw, :mswin, :x64_mingw, :jruby]

# Deal with command injection vulnerability in Nokogiri v1.10.3 and earlier versons
# (https://github.com/advisories/GHSA-cr5j-953j-xw5p)
gem "nokogiri", ">= 1.10.4"
