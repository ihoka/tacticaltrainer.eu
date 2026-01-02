# frozen_string_literal: true

source 'https://rubygems.org'

gem 'jekyll', '~> 4.3'

# Ruby 4.0 compatibility - these gems were removed from stdlib
gem 'base64'
gem 'bigdecimal'
gem 'csv'
gem 'logger'
gem 'minima', '~> 2.5'

# Jekyll plugins
group :jekyll_plugins do
  gem 'jekyll-feed', '~> 0.12'
  gem 'jekyll-paginate', '~> 1.1'
  gem 'jekyll-seo-tag', '~> 2.8'
  gem 'jekyll-sitemap', '~> 1.4'
end

# Windows and JRuby does not include zoneinfo files
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem 'tzinfo', '>= 1', '< 3'
  gem 'tzinfo-data'
end

# Lock http_parser.rb gem to v0.6.x on JRuby builds
gem 'http_parser.rb', '~> 0.6.0', platforms: [:jruby]
