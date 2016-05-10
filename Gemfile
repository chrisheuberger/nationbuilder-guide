# Skinny Bones Gemfile
source "https://rubygems.org"

gem "jekyll", "~> 3.0"
gem "jekyll-sitemap"
gem "jekyll-gist"
gem "octopress", "~> 3.0"

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages']
gem 'jekyll-sass-converter'
