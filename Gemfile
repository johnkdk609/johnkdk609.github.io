source "https://rubygems.org"

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
gem "jekyll", "~> 3.9.3"

gem "jekyll-theme-hydejack", "~> 9.1"

# IMPORTANT: The followign gem is used to compile math formulas to 
# KaTeX during site building.
#
# There are a couple of things to know about this gem:
# *  It is not supported on GitHub Pages. 
#    You have to build the site on your machine before uploading to GitHub,
#    or use a more permissive cloud building tool such as Netlify.
# *  You need some kind of JavaScript runtime on your machine.
#    Usually installing NodeJS will suffice. 
#    For details, see <https://github.com/kramdown/math-katex#documentation>
#
# If you're using the MathJax math engine instead, free to remove the line below:
gem "kramdown-math-katex"

# A JavaScript runtime for ruby that helps with running the katex gem above.
gem "duktape"

# Fixes `jekyll serve` in ruby 3
gem "webrick"

group :jekyll_plugins do
  gem "jekyll-default-layout", "~> 0.1.4"
  gem "jekyll-feed", "~> 0.15.1"
  gem "jekyll-optional-front-matter", "~> 0.3.2"
  gem "jekyll-paginate", "~> 1.1.0"
  gem "jekyll-readme-index", "~> 0.3.0"
  gem "jekyll-redirect-from", "~> 0.16.0"
  gem "jekyll-relative-links", "~> 0.6.1"
  gem "jekyll-seo-tag", "~> 2.8.0"
  gem "jekyll-sitemap", "~> 1.4.0"
  gem "jekyll-titles-from-headings", "~> 0.5.3"
  gem "jekyll-include-cache", "~> 0.2.1"

  # Non-Github Pages plugins:
  gem "jekyll-last-modified-at", "~> 1.0.0"
  gem "jekyll-compose", "~> 1.0.0"
end

gem 'wdm' if Gem.win_platform?
gem "tzinfo-data" if Gem.win_platform?
