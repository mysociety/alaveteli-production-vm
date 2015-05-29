# Welcome! Thanks for taking an interest in contributing to Alaveteli.
# This Vagrantfile runs the install script in "production" mode rather than
# for a development VM.
#
# Usage
# =====
#
# TODO
#
# Customizing the Vagrant instance
# ================================
#
# This Vagrantfile allows customisation of some aspects of the virtaual machine
# See the customization options below for details.
#
# The options can be set either by prefixing the vagrant command, or by
# exporting to the environment.
#
#   # Prefixing the command
#   $ ALAVETELI_VAGRANT_MEMORY=2048 vagrant up
#
#   # Exporting to the environment
#   $ export ALAVETELI_VAGRANT_MEMORY=2048
#   $ vagrant up
#
# Both have the same effect, but exporting will retain the variable for the
# duration of your shell session.
#
# Customization Options
# =====================
ALAVETELI_FQDN = ENV['ALAVETELI_VAGRANT_FQDN'] || "192.168.33.156"
ALAVETELI_IP = ENV['ALAVETELI_VAGRANT_IP'] || "192.168.33.156"
ALAVETELI_MEMORY = ENV['ALAVETELI_VAGRANT_MEMORY'] || 1536
ALAVETELI_OS = ENV['ALAVETELI_VAGRANT_OS'] || 'wheezy64'

SUPPORTED_OPERATING_SYSTEMS = {
  'precise64' => 'https://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box',
  'squeeze64' => 'http://puppet-vagrant-boxes.puppetlabs.com/debian-607-x64-vbox4210-nocm.box',
  'wheezy64' => 'http://puppet-vagrant-boxes.puppetlabs.com/debian-73-x64-virtualbox-nocm.box'
}

def box
  ALAVETELI_OS
end

def box_url
  SUPPORTED_OPERATING_SYSTEMS[ALAVETELI_OS]
end

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = box
  config.vm.box_url = box_url
  config.vm.network :private_network, :ip => "#{ ALAVETELI_IP }"

  config.ssh.forward_agent = true

  # The bundle install fails unless you have quite a large amount of
  # memory; insist on 1.5GiB:
  config.vm.provider "virtualbox" do |vb|
    host = RbConfig::CONFIG['host_os']
    # Give VM access to all cpu cores on the host
    if host =~ /darwin/
      cpus = `sysctl -n hw.ncpu`.to_i
    elsif host =~ /linux/
      cpus = `nproc`.to_i
    else # sorry Windows folks, I can't help you
      cpus = 1
    end

    vb.customize ["modifyvm", :id, "--memory", ALAVETELI_MEMORY]
    vb.customize ["modifyvm", :id, "--cpus", cpus]
  end

  # Fetch and run the install script:
  config.vm.provision :shell, :inline => "apt-get -y install curl"
  config.vm.provision :shell, :inline => "curl -O https://raw.githubusercontent.com/mysociety/commonlib/master/bin/install-site.sh"
  config.vm.provision :shell, :inline => "chmod a+rx install-site.sh"
  config.vm.provision :shell, :inline => "./install-site.sh " \
                                             "--default " \
                                             "alaveteli " \
                                             "alaveteli " \
                                             "#{ ALAVETELI_FQDN }"

  config.vm.provision :shell, :inline => %Q(sed -r -i -e "s,^( *STAGING_SITE:).*,\\1 '0'," /var/www/alaveteli/alaveteli/config/general.yml)
  config.vm.provision :shell, :inline => %Q(sed -r -i -e "s,^( *RAILS_ENV=).*,\\1production," /etc/init.d/alaveteli)
  config.vm.provision :shell, :inline => %Q(sudo -u alaveteli echo "ENV['RAILS_ENV'] ||= 'production'" > /var/www/alaveteli/alaveteli/config/rails_env.rb)

  # Re-run the installer to ensure the production settings are applied and
  # production database created etc
  config.vm.provision :shell, :inline => "./install-site.sh " \
                                             "--default " \
                                             "alaveteli " \
                                             "alaveteli " \
                                             "#{ ALAVETELI_FQDN }"
end
