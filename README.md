
# Overview

These blueprints provide an easy way to ensure hostnames resolve wherever needed in an Apache Brooklyn blueprint. 
This pattern can work in any cloud or in containers, and is extremely configurable and extensible.

To use, nodes simply advertise `brooklyn_dns.enabled` and will automatically be placed under DNS management,
either using /etc/hosts or a dedicated BIND DNS server.  


## Synchronizing /etc/hosts

One approach is to have Brooklyn update the `/etc/hosts` on all the servers you are interested in,
containing the IPs and hostnames for all the servers you're interested in.

To use this, simply add this to your blueprint:

```
  - type: brooklyn-dns-etc-hosts-generator
```

And then add this bit of config to every entity (or ancestor whose children you're interested in):

```
    brooklyn.config:
      # tells the etc-hosts-generator to manage DNS on this server
      brooklyn_dns.enabled: true
```


As an example, see [brooklyn-dns-etc-hosts-sample.yaml](brooklyn-dns-etc-hosts-sample.yaml).


## Using Your Own DNS Server

A more scalable approach is to specify a DNS server to use on each node and update a DNS server.

You can have Apache Brooklyn create a BIND DNS server for you, by including the
[brooklyn_dns-bind-server.yaml](brooklyn_dns-bind-server.yaml) blueprint.
You can have VMs registered with that DNS server by setting `brooklyn_dns.enabled: true`
(as in the `/etc/hosts` example).
And you can tell VMs to use the BIND DNS server by adding
[brooklyn-dns-registration-hook.yaml](brooklyn-dns-registration-hook.yaml) as a child
to any machine entity.
 
This is illustrated in
[brooklyn-dns-bind-and-registration-sample.yaml](brooklyn-dns-bind-and-registration-sample.yaml).



## When to Use Which?

The former is nice in a lightweight environment as it introduces no additional dependencies.
The latter is better in a larger environment and where nodes change frequently, to avoid SSHing to each node.
In the latter case there is an additional requirement
to add a child entity to any node which will need to *use* that DNS server.


# Future Work

This is all TODO.

* subnet addresses -- in bind, and /etc/hosts
* Allow an entity to specify multiple hostnames, configuring round-robin DNS as a result
* Clustered BIND servers (easy, it's just a group, collecting the IP's and inserting them at nodes;
  slightly trickier to randomize the list at each node and to update the BIND configuration at nodes)
* Configure TTL for BIND
* Policy for BIND servers to add the registration-hook automatically
