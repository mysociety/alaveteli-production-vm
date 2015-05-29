# Alaveteli Production Test VM

VM with Alaveteli installed in production mode for testing.

**We do not recommend Vagrant for real production installs.**

## Usage

    git clone https://github.com/mysociety/alaveteli-production-vm.git
    cd alaveteli-production-vm
    vagrant up

You could use a one-liner to destroy the VM, boot it, start alaveteli and open
it in your browser:

    vagrant destroy -f && vagrant up && vagrant ssh -c 'sudo service alaveteli start' && sleep 15 && open http://192.168.33.156

## Gotchas

The alaveteli service is not started by default
[mysociety/alaveteli#2252](https://github.com/mysociety/alaveteli/issues/2252).
To start the service run `sudo service alaveteli start`.

---

The service is started in development mode
[mysociety/alaveteli#2392](https://github.com/mysociety/alaveteli/issues/2392).
This issue is worked around by additional vagrant provision scripts (1613df3,
d0985bf).
