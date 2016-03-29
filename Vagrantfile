Vagrant.configure('2') do |config|

  config.vm.box = 'ubuntu/trusty64'
  config.vm.box_check_update = false

  if Vagrant.has_plugin?('vagrant-omnibus')
    config.omnibus.chef_version = 'latest'
  end

  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.scope = :box
    config.cache.auto_detect = false
    config.cache.enable :apt
    config.cache.enable :yum
    config.cache.enable :gem
    config.cache.enable :chef_gem
    config.cache.enable :generic, { :cache_dir => '/var/chef/cache' }
  end

  config.vm.define "dev" do |dev|
    dev.vm.hostname = "dev"
    dev.vm.network :private_network, ip: '192.168.33.100'

    dev.vm.provider 'virtualbox' do |vb|
      vb.memory = 1024
    end

    dev.vm.provision :chef_zero, install: true do |chef|
      chef.verbose_logging
      chef.nodes_path = 'cookbooks'
      chef.file_cache_path = '/var/chef/cache'
      chef.add_recipe 'sample-app::default'
      chef.json = {}
    end
  end

  config.vm.define "ci" do |ci|
    ci.vm.hostname = "ci"
    ci.vm.network :private_network, ip: '192.168.33.101'

    ci.vm.provider 'virtualbox' do |vb|
      vb.memory = 1024
    end

    ci.vm.provision :chef_zero, install: true do |chef|
      chef.verbose_logging
      chef.nodes_path = 'cookbooks'
      chef.file_cache_path = '/var/chef/cache'
      chef.add_recipe 'ci::default'
      chef.json = {}
    end
  end

  ['test', 'production'].each_with_index do |environment, index|
    config.vm.define "#{environment}" do |machine|
      machine.vm.hostname = "#{environment}"
      machine.vm.network :private_network, ip: "192.168.33.#{102 + index}"

      machine.vm.provider 'virtualbox' do |vb|
        vb.memory = 1024
      end

      machine.vm.provision :chef_zero, install: true do |chef|
        chef.verbose_logging
        chef.nodes_path = 'cookbooks'
        chef.file_cache_path = '/var/chef/cache'
        chef.add_recipe 'sample-app::default'
        chef.add_recipe 'sample-app::nginx'
        chef.add_recipe 'sample-app::application_deployment'
        chef.add_recipe 'logging::client'
        chef.json = {
          "databases": ["db_notes"],
          "logging": {
              "host": "192.168.33.110"
          }
        }
      end
    end
  end

  config.vm.define "management" do |management|
    management.vm.hostname = "management"
    management.vm.network :private_network, ip: '192.168.33.110'

    management.vm.provider 'virtualbox' do |vb|
      vb.memory = 1024
    end

    management.vm.provision :chef_zero, install: true do |chef|
      chef.verbose_logging
      chef.nodes_path = 'cookbooks'
      chef.file_cache_path = '/var/chef/cache'
      chef.add_recipe 'logging::default'
      chef.json = {}
    end
  end

end
