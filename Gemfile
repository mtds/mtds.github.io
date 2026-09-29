source "https://rubygems.org"

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# The ruby version is not pinned here on purpose: GitHub Pages builds
# with its own Ruby toolchain, and the local lockfile is generated in
# a ruby:3.3 container (see Gemfile.lock).

# This is the default theme for new Jekyll sites.
gem "minima", "~> 2.0"

gem "jekyll"
gem "jekyll-remote-theme"

# The following gems are required by _config.yml (plugins + remote theme).
# They used to be pulled in transitively via the github-pages gem, which was
# removed because it pins jekyll-remote-theme to 0.4.3, capping rubyzip at
# < 3.0 (see CVE-2026-85396).
gem "jekyll-paginate"
gem "jekyll-sitemap"
gem "kramdown-parser-gfm"

# Bump to 3.4.0+ to fix CVE-2026-85396 (path traversal in Zip::Entry#extract).
gem "rubyzip", ">= 3.4.0"

# If you have any plugins, put them here!
group :jekyll_plugins do
   gem "jekyll-feed", "~> 0.17"
   gem "jekyll-gist"
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem "tzinfo-data", platforms: [:mingw, :mswin, :x64_mingw, :jruby]
