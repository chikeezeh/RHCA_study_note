#### Using filters to work with network addresses.

The `ipaddr` filter is used when working with IP addresses. To use this filter, the following packages needs to be installed; `python3-netaddr` and `python3-dns`. This filter comes from the `ansible.utils` collection. So install with `ansible-galaxy collection install ansible.utils`.

The filter has arguments for the following:
- `address` validates the input is and IP address.
- `net` checks that input values are network ranges.
- `host` ensures the IP addresses conform to CIDR prefix format. 
- `prefix` returns the network address prefix.

