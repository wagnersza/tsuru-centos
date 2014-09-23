Vagrant.configure("2") do |config|

  config.vm.box      = 'CentOS-7.0-x86_64-minimal'
  config.vm.box_url  = 'iso/CentOS-7.0-x86_64-minimal.box'

  # Redis Server
  config.vm.define :tsuru_redis_master do |tsuru|
  
    tsuru.vm.hostname = 'tsuru-redis-master'
    tsuru.vm.network 'private_network', ip: '10.200.200.10'

    tsuru.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--name", "tsuru_redis_master"]
    end

    tsuru.vm.provision :puppet do |puppet|
      puppet.manifests_path = "manifests"
      puppet.module_path    = "modules"
      puppet.manifest_file  = "tsuru_redis_master.pp"
      puppet.facter = { "fqdn"                => "tsuru-redis-master",
                        "tsuru_redis_master"  => "10.200.200.10",
                        "tsuru_redis_servers" => "10.200.200.10 localhost"
                       }
    end
  end

  # Hipache
  config.vm.define :tsuru_router do |tsuru|

    tsuru.vm.hostname = 'tsuru-router'
    tsuru.vm.network 'private_network', ip: '10.200.200.11'

    tsuru.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "512"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--name", "tsuru_router"]
    end

    tsuru.vm.provision :puppet do |puppet|
      puppet.manifests_path = "manifests"
      puppet.module_path    = "modules"
      puppet.manifest_file  = "tsuru_router.pp"
      puppet.facter = { "tsuru_redis_master" => "10.200.200.10" }
    end
  end

  # Docker
  config.vm.define :tsuru_docker do |tsuru|
    tsuru.vm.hostname = 'docker'
    tsuru.vm.network :private_network, ip: '10.200.200.12'

    tsuru.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--name", "tsuru_docker"]
    end

    # tsuru.vm.provision :puppet do |puppet|
    #   puppet.manifests_path = "manifests"
    #   puppet.module_path    = "modules"
    #   puppet.manifest_file  = "tsuru_docker.pp"
    # end
  end

  # Gandalf - Registry
  config.vm.define :tsuru_registry do |tsuru|
    tsuru.vm.hostname = 'tsuru-registry'
    tsuru.vm.network :private_network, ip: '10.200.200.13'

    tsuru.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "512"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--name", "tsuru_registry"]
    end

    tsuru.vm.provision :puppet do |puppet|
      puppet.manifests_path = "manifests"
      puppet.module_path    = "modules"
      puppet.manifest_file  = "tsuru_registry.pp"
      puppet.facter         = { "gandalf_host"         => "git.tsuru.globoi.com",
                                "gandalf_ipbind_port"  => "0.0.0.0:8080",
                                "gandalf_db_url"       => "0.0.0.0:27001",
                              }
    end
  end

  # Tsuru API
  config.vm.define :tsuru_api do |tsuru|
    tsuru.vm.hostname = 'tsuru-api'
    tsuru.vm.network :private_network, ip: '10.200.200.14'

    tsuru.vm.provider :virtualbox do |vb|
      vb.customize ["modifyvm", :id, "--memory", "512"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--name", "tsuru_api"]
    end

    tsuru.vm.provision :puppet do |puppet|
      puppet.manifests_path = "manifests"
      puppet.module_path    = "modules"
      puppet.manifest_file  = "tsuru_api.pp" 

      puppet.facter = {
                        "tsuru_server_domain"    => 'test.tsuru.local',
                        "tsuru_redis_master"     => '10.200.200.10:6379',
                        "tsuru_registry_server"  => '10.200.200.13',
                        "tsuru_git_url"          => "http://10.200.200.13",
                        "tsuru_git_rw_host"      => "10.200.200.13",
                        "tsuru_git_ro_host"      => "10.200.200.13",
                        "tsuru_docker_servers"   => "10.200.200.12:4243",
                        "tsuru_collector_server" => "tsuru-api"
                      }
    end
  end

end
