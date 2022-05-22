# Notes

## Setup

- How to setup Portainer: [Portainer Setup](https://docs.portainer.io/v/ce-2.9/start/install/server/docker/linux)
- How to setup Cockpit: [Cockpit Setup](https://cockpit-project.org/running.html#ubuntu)

## AdGuard Home

### IPV6

TODO: Figure out how to get IPV6 working over DHCP and Docker

[How-to: Use IPv6 with Portainer on Docker](https://www.youtube.com/watch?v=FOFTEsgxro4)
[Enable IPv6 support](https://docs.docker.com/config/daemon/ipv6/)


### MacVlan

AdGuard should be run as a MacVlan network in order to use it as DNS and DHCP server.

[How To Setup MacVLAN in Portainer](https://www.youtube.com/watch?v=o7nn6Tv-PAw)
[Docker MacVlan The Correct Way](https://www.reddit.com/r/selfhosted/comments/rzbz6h/docker_macvlan_the_correct_way/)

### resolved

If you try to run AdGuardHome on a system where the `resolved` daemon is started, docker will fail to bind on port 53, because `resolved` daemon is listening on `127.0.0.53:53`. Here's how you can disable `DNSStubListener` on your machine:

1. Deactivate `DNSStubListener` and update the DNS server address. Create a new file, `/etc/systemd/resolved.conf.d/adguardhome.conf` (creating the `/etc/ systemd/resolved.conf.d` directory if needed) and add the following content to it:

    ```
    [Resolve]
    DNS=127.0.0.1
    DNSStubListener=no    
    ```

    Specifying `127.0.0.1` as the DNS server address is necessary because otherwise the nameserver will be `127.0.0.53` which doesn't work without `DNSStubListener`.

2. Activate a new `resolv.conf` file:
    ```
    mv /etc/resolv.conf /etc/resolv.conf.backup
    ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
    ```

3. Stop `DNSStubListener`:    

    `systemctl reload-or-restart systemd-resolved`