source 'https://rubygems.org'

gem 'chef', '~> 11.16.4'
gem 'json', '<= 1.7.7' # chef dependency
# gem 'knife-block', '~> 0.0.6'
# gem 'spiceweasel', '~> 2.6.0'
gem 'berkshelf', '3.2.2'
gem 'hashie', '~> 2.0'

group :development do
  # We depend on Vagrant for development, but we don't add it as a
  # gem dependency because we expect to be installed within the
  # Vagrant environment itself using `vagrant plugin`.
  gem "vagrant", :git => "git://github.com/mitchellh/vagrant.git", :tag => "v1.6.5"

  # gem "vagrant", :path => '~/active/vagrant'
  gem "byebug", :require => true
end

group :plugins do
  # gem "vagrant-berkshelf", "4.0.0"
  gem "vagrant-chef-zero", :path => '~/active/vagrant-chef-zero'
  gem "vagrant-omnibus"
end