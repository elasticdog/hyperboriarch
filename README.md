Hyperboriarch
=============

Hyperboriarch is a set of [Ansible](http://www.ansibleworks.com/)
playbooks for installing, configuring, and maintaining
[Project Meshnet](https://projectmeshnet.org/)'s
[cjdns](https://github.com/cjdelisle/cjdns#readme) routing software on
[Arch Linux](https://www.archlinux.org/) hosts. It can also perform the
basic tasks for securing a freshly installed server, so you can go from
nothing to a running cjdns node in no time flat. Welcome to
[Hyperboria](http://hyperboria.net/)!

Overview
--------

If you apply the entire _site.yml_ playbook to your hosts, the following
things will be configured for you by the "common" role:

* an updated pacman mirrorlist will be downloaded
* an non-root administrative user will be created
* OpenSSH will be configured more securely
* basic firewall rules will be put into place
* kernel parameters will be set to harden the network stack
* the Network Time Protocol (NTP) service will be configured
* vnStat will be installed to monitor network traffic usage

...and the following things will be configured for you by the "cjdns"
role:

* cjdns and (optionally) [cjdcmd](https://github.com/inhies/cjdcmd#readme)
  will be built and installed from the latest upstream git revision
* a host specific _cjdroute.conf_ file will be copied over from the
  controlling machine

There are a lot of little conveniences automatically handled for you as
well. For instance, the mirrorlist will only be updated on the initial
run, the firewall will open up your cjdns port if it finds
a _cdjroute.conf_ file, cdjcmd will generate the required _~/.cjdadmin_
file for you if possible, etc..

_Note that you don't have to apply all of these tasks; everything is
[tagged](http://www.ansibleworks.com/docs/playbooks2.html#tags) for
flexibility._

Initial Setup
-------------

    $ git clone https://github.com/elasticdog/hyperboriarch.git
    $ cd hyperboriarch/

### Prerequisites

* Ansible v1.3+
* Arch Linux host(s)
* root-level access on the host(s), directly or via sudo
* an [inventory file](http://www.ansibleworks.com/docs/patterns.html)

For the inventory hosts file, you can use the default location or simply
keep it in the same directory as Hyperboriarch. If you choose the latter,
either set the `ANSIBLE_HOSTS` environment variable to point to it, or use
the `-i <inventory file>` flag on all of your Ansible commands.

### Bootstrapping

Arch Linux doesn’t have Python v2 installed by default, which is
a dependency for Ansible. Luckily we can use the
[raw module](http://ansibleworks.com/docs/modules.html#raw) to fix that:

> For the bootstrapping instructions, I'm assuming that you can connect to
> your hosts as the _root_ user...adjust these commands as necessary.

    $ ansible all -m raw -a '/usr/bin/pacman -Sy --noconfirm python2' --user=root

Always having to specify the user can get annoying, and using _root_
directly isn't the most secure practice. Instead we can create a new
administrative user based on your current username and
[ssh key](https://wiki.archlinux.org/index.php/SSH_keys) by running just
the "bootstrap" tagged tasks from the playbook:

    $ ansible-playbook site.yml --tags=bootstrap --user=root

Now you should be able to connect as yourself for all future tasks:

    $ ansible all -m ping

### cjdroute.conf

The last thing we need to worry about is the _/etc/cjdroute.conf_ file
that cjdns needs in order to run. It should be unique per host and
contains your cryptographic key pair and network peering data.

##### Use an Existing Config

If you already have a _cjdroute.conf_ file, simply copy it to the expected
location on the controlling machine and Ansible will push it to the proper
host:

    $ cp <path-to>/cjdroute.conf roles/cjdns/files/{{ inventory_hostname }}-cjdroute.conf

As a concrete example, let's say you're managing a host
"alice.example.com", and you have a _cjdroute.conf_ file for it in your
home directory:

    $ cp ~/cjdroute.conf roles/cjdns/files/alice.example.com-cjdroute.conf
    $ ansible-playbook site.yml

##### Generate a New Config

If you're starting from scratch, you can generate a config with the
`cjdroute` command on the managed host(s) _after_ you've done your first
playbook run (expect a failure):

    $ ansible-playbook site.yml
    ...
    FATAL: all hosts have already failed -- aborting
    ...
    $ ansible all -m shell -a "[[ -f /etc/cjdroute.conf ]] || /usr/bin/cjdroute --genconf > /tmp/cjdroute.conf"
    $ ansible all -m fetch -a "src=/tmp/cjdroute.conf dest=roles/cjdns/files/{{ inventory_hostname }}-cjdroute.conf flat=yes"
    $ ansible-playbook site.yml

### Firewall Considerations

At this point, cjdns should be up and running on your host(s), but will
still have incoming connections blocked by the firewall. Now that we've
pushed a configuration file, Hyperboriarch can determine which port needs
to be opened up. Run the "iptables" tagged tasks one final time to make
the change:

    $ ansible-playbook site.yml --tags=iptables

Updating cjdns
--------------

The cjdns project is very much alpha, and still under heavy development,
so it's best to keep up-to-date with all of the upstream changes. You can
easily rebuild the latest version and upgrade your systems by running the
"cjdns" tagged tasks (or the entire playbook, if you prefer):

    $ ansible-playbook site.yml --tags=cjdns

Options
-------

Most of the tasks performed by Hyperboriarch can be customized by
overriding variables. The default values for everything that can be
tweaked are stored and explained in the `group_vars/all` file on the
controlling machine.

If you just want to customize the values for a single run, you can use the
`--extra-vars` flag:

    $ ansible-playbook site.yml --extra-vars="update_mirrorlist=true"

License
-------

Hyperboriarch is provided under the terms of the
[ISC License](https://en.wikipedia.org/wiki/ISC_license).

Copyright &copy; 2013, [Aaron Bull Schaefer](mailto:aaron@elasticdog.com).
