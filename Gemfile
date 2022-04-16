source "https://rubygems.org"
gemspec
group :jekyll_plugins do
    gem "jekyll-sitemap"
end

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'html-proofer'
gem 'tzinfo'
gem 'tzinfo-data'
gem 'jekyll-remote-theme'
gem 'kramdown', versions['kramdown']
gem 'github-pages', versions['github-pages']
gem 'rake'
gem 'jekyll-feed'
