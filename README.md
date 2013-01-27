# Piwiker [![Build Status](https://travis-ci.org/Achillefs/autometal-piwik.png?branch=v1.x)](https://travis-ci.org/Achillefs/autometal-piwik)

Version 1.0.0 is a ground-up rewrite of the older [autometal-piwik gem](https://rubygems.org/gems/autometal-piwik), itself based [on work](http://github.com/riopro/piwik) by Rodrigo Tassinari de Oliveira. It aspires to completely cover the Piwik API and be easily extendable by its users. To achieve that, we will not be making any assumptions on how you wanna use your data, so we will closely mirror Piwik's API structure even if it looks kinda weird to a modern rubyist.

We will also implement an extended `Piwik::Site` wrapper class that will give you ruby-friendly access to a lot of the data in a way we think is sane, but it will be up to you which interface you want to use.

## Installation

Add this line to your application's Gemfile:

    gem 'piwiker', :require => 'piwik'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install piwiker

## Usage

### Initialize it
    require 'rubygems'
    require 'piwiker'
    Piwik::PIWIK_URL = 'http://demo.piwik.org'
    Piwik::PIWIK_TOKEN = 'anonymous'

### Use the wrapper class
Fastest way to get to know the client is by using the Piwik::Site wrapper class:
    
    site = Piwik::Site.load(7)
    => #<Piwik::Site[snip]>
    
    site.annotations.all
    => #<Piwik::Annotations[snip]>
    site.annotations.add(:date => 'today', :starred => 1)
    => Piwik::ApiError: Please specify a value for 'note'.
    site.annotations.add(:note => 'meep', :date => 'today', :starred => 1)
    => #<Piwik::Annotations[snip]>
    
    summary = site.actions.summary
    => #<Piwik::Actions[snip]>
    summary.nb_pageviews
    => 236 
    summary.nb_uniq_pageviews
     => 170

Not all methods are implemented on the wrapper class, although if you find yourself adding methods, please submit a pull request.
You can have a look at [site_spec.rb](https://github.com/Achillefs/autometal-piwik/blob/v1.x/spec/site_spec.rb) for an up-to-date list of available methods.

### Use the 'native' API
This client also allows you to interact with the API the way its designers wanted it. Any method in the [api reference](http://piwik.org/docs/analytics-api/reference/) is made available almost verbatim:

    # http://piwik.org/docs/analytics-api/reference/#Actions
    # Actions.getPageUrls (idSite, period, date, segment = '', expanded = '', idSubtable = '')
    require 'rubygems'
    require 'piwiker'
    Piwik::PIWIK_URL = 'http://demo.piwik.org'
    Piwik::PIWIK_TOKEN = 'anonymous'
    Piwik::Actions.getPageUrls(:idSite => 7, :period => :day, :date => 'yesterday')
    => #<Piwik::Actions::PageUrls @data=[snip]>

That last call is exactly the same as calling `site.actions.page_urls(:period => :day, :date => 'yesterday')`

It is probably apparent, but the second way gives you full access to everything, all you need is the API reference and you're off. If you are simply after displaying the basic analytics values for a site, the wrapper is probably the way to go.

For more information on Piwik and it’s API, see the [Piwik website](piwik.org) and the [Piwik API reference](http://piwik.org/docs/analytics-api/reference/).

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
