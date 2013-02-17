# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant::Config.run do |config|

  config.vm.define :server do |server_config|
    # Every Vagrant virtual environment requires a box to build off of.
    server_config.vm.box = "ubuntu-12.04"

    # Add a little more memory to allow for internal hosts
    server_config.vm.customize ["modifyvm", :id, "--memory", 3072]

    server_config.vm.network :hostonly, "192.168.33.11"
    server_config.vm.host_name = "vagrant-server.vm"

    {
     8080 => 8080,  # Nagios web frontend
     8081 => 8081,  # Graphite
     8082 => 8082,  # Collectd web
     8083 => 8083,  # Kibana
     8084 => 8084,  # Nginx Tasseo
     5000 => 8085,  # Tasseo
     # Statsd telnet interface (see https://github.com/etsy/statsd)
     # telnet localhost 8086
     # use commands 'stats' 'counters' or 'timers'
     8126 => 8086,  # Statsd
     1105 => 8087,  # JMX
     9200 => 8088,  # ES
     55672 => 8089, # RabbitMQ
     8090 => 8090,  # Sensu dashboard
     8091 => 8091   # Sensu API
    }.each do |local_port,external_port|
      server_config.vm.forward_port local_port, external_port
    end

    # We need to increase timeout due to the forward ports default = 10
    server_config.ssh.timeout = 1000
    server_config.ssh.max_tries = 3000

    server_config.vm.provision :shell, :path => "definitions/ubuntu-12.04/chef-client.sh"

    # server_config.vm.share_folder "v-data", "/vagrant_data", "../data"

    server_config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = [ "cookbooks" , "monigusto/cookbooks","site-cookbooks"]
      chef.roles_path = "roles"
      chef.data_bags_path = "data_bags"
      chef.add_role "monigusto_server"
    end
  end

  config.vm.define :client do |client_config|
    # Every Vagrant virtual environment requires a box to build off of.
    client_config.vm.box = "ubuntu-12.04"

    # Add a little more memory to allow for internal hosts
    client_config.vm.customize ["modifyvm", :id, "--memory", 1024]

    client_config.vm.network :hostonly, "192.168.33.10"
    client_config.vm.host_name = "vagrant-client.vm"

    # client_config.vm.network :bridged

    # client_config.vm.forward_port 80, 8080

    # client_config.vm.share_folder "v-data", "/vagrant_data", "../data"
    client_config.vm.provision :shell, :path => "definitions/ubuntu-12.04/chef-client.sh"

    client_config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = [ "cookbooks" , "monigusto/cookbooks","site-cookbooks"]
      chef.roles_path = "roles"
      chef.log_level = :debug
      chef.data_bags_path = "data_bags"
      chef.add_role "monigusto_client"
    end
  end

end
