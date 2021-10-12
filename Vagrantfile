# -*- mode: ruby -*-
# vi: set ft=ruby :
# Run with: vagrant up --provider=docker
# Generate a random port number
vagrant_root = File.dirname(__FILE__)
dev_domain = 'proxiblue.test'
ip_range = ENV['DEV_IP_RANGE'] || "172.40.1"
puts "========================================================"
puts "domain : #{dev_domain}"
puts "folder : #{vagrant_root}"
puts "ip range used: #{ip_range}"
puts "========================================================"

Vagrant.configure('2') do |config|
    config.vm.boot_timeout = 300
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
    config.hostmanager.ignore_private_ip = false
    config.hostmanager.include_offline = false
    config.vm.network "private_network", type: "dhcp"
    config.vm.network "forwarded_port", guest: 22, host: Random.new.rand(1000...5000), id: 'ssh', auto_correct: true

    config.vm.define "cypress", primary: false do |cypress|
        cypress.hostmanager.aliases = [ "cypress."+dev_domain ]
        cypress.vm.network :private_network, ip: "#{ip_range}.201", subnet: "#{ip_range}.0/16"
        cypress.vm.hostname = "cypress#{dev_domain}"
        cypress.ssh.insert_key = true
        cypress.ssh.username = "vagrant"
        cypress.ssh.password = "vagrant"
        #cypress.vm.communicator = 'docker'
        cypress.vm.provider 'docker' do |d|
            d.build_dir = "#{vagrant_root}/docker/cypress/"
            d.dockerfile = "Dockerfile"
            d.has_ssh = true
            d.name = "cypress_#{dev_domain}"
            d.remains_running = true
            d.volumes = ["/tmp/.X11-unix:/tmp/.X11-unix"]
        end
    end

    config.vm.define "web", primary: true do |box|
        box.hostmanager.aliases = [ "test."+dev_domain ]
        box.vm.network :private_network, ip: "#{ip_range}.200", subnet: "#{ip_range}.0/16"
        box.vm.hostname = "web#{dev_domain}"
        box.ssh.insert_key = true
        box.ssh.username = "vagrant"
        box.ssh.password = "vagrant"
        box.vm.provider 'docker' do |d|
            d.build_dir = "#{vagrant_root}/docker/web/"
            d.dockerfile = "Dockerfile"
            d.has_ssh = true
            d.name = "web_#{dev_domain}"
            d.create_args = ["--cap-add=NET_ADMIN"]
            d.remains_running = true
            d.volumes = [ENV['HOME']+"/.ssh/:/home/vagrant/.ssh", "/tmp/.X11-unix:/tmp/.X11-unix", "#{vagrant_root}/nginx:/etc/nginx/sites-enabled"]
        end
        ## FINAL BOX MUST HAVE THIS
        box.trigger.after :up do |trigger|
            trigger.run = {inline: "bash -c 'vagrant hostmanager --provider docker'"}
        end
        ##
    end

end
