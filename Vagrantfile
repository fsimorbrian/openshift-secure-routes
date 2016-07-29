# -*- mode: ruby -*-
# vi: set ft=ruby :

# INFO(bmoriarty)
# This is a modified version of the example Vagrant file provided with the OpenShift CDK 2.1
# ~/cdk/components/rhel/rhel-ose/Vagrantfile

# The private network IP of the VM. You will use this IP to connect to OpenShift.
PUBLIC_ADDRESS="10.1.2.2"

# Number of virtualized CPUs
VM_CPU = ENV['VM_CPU'] || 2

# Amount of available RAM
VM_MEMORY = ENV['VM_MEMORY'] || 3072

REPOSITORY_DIR = '/vagrant'

# Validate required plugins
REQUIRED_PLUGINS = %w(vagrant-service-manager vagrant-registration)
errors = []

def message(name)
  "#{name} plugin is not installed, run `vagrant plugin install #{name}` to install it."
end
# Validate and collect error message if plugin is not installed
REQUIRED_PLUGINS.each { |plugin| errors << message(plugin) unless Vagrant.has_plugin?(plugin) }
unless errors.empty?
  msg = errors.size > 1 ? "Errors: \n* #{errors.join("\n* ")}" : "Error: #{errors.first}"
  fail Vagrant::Errors::VagrantError.new, msg
end

Vagrant.configure(2) do |config|
  config.vm.box = "cdkv2"

  config.vm.provider "virtualbox" do |v, override|
    v.memory = VM_MEMORY
    v.cpus   = VM_CPU
    v.customize ["modifyvm", :id, "--ioapic", "on"]
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  config.vm.provider "libvirt" do |v, override|
    v.memory = VM_MEMORY
    v.cpus   = VM_CPU
    v.driver = "kvm"
  end

  config.vm.network "private_network", ip: "#{PUBLIC_ADDRESS}"

  if ENV.has_key?('SUB_USERNAME') && ENV.has_key?('SUB_PASSWORD')
    config.registration.username = ENV['SUB_USERNAME']
    config.registration.password = ENV['SUB_PASSWORD']
  end

  config.servicemanager.services = "docker"

  config.vm.provision "shell", run: "always", inline: <<-SHELL
    systemctl enable openshift 2>&1
    systemctl start openshift
  SHELL

  config.vm.provision "shell", run: "always", inline: <<-SHELL
    echo
    echo "Successfully started and provisioned VM with #{VM_CPU} cores and #{VM_MEMORY} MB of memory."
    echo "To modify the number of cores and/or available memory set the environment variables"
    echo "VM_CPU respectively VM_MEMORY."
    echo
    echo "You can now access the OpenShift console on: https://#{PUBLIC_ADDRESS}:8443/console"
    echo
    echo "To use OpenShift CLI, run:"
    echo "$ vagrant ssh"
    echo "$ oc login #{PUBLIC_ADDRESS}:8443"
    echo
    echo "Configured users are (<username>/<password>):"
    echo "openshift-dev/devel"
    echo "admin/admin"
    echo
    echo "If you have the oc client library on your host, you can also login from your host."
    echo
  SHELL

  config.vm.provision "shell", run: "once", inline: <<-SHELL
    sudo -i -uvagrant oc new-project \
      "openshift-secure-routes" --display-name=secure-sample --description="example services to test secure routes"

    sudo -i -uvagrant oc new-app \
             registry.access.redhat.com/jboss-webserver-3/webserver30-tomcat8-openshift \
            --name=mywebapp

    sudo -i -uvagrant oc new-app https://github.com/fsimorbrian/openshift-secure-routes \
            --context-dir=nginx --strategy=docker \
            --name=mynginx
  SHELL
end
