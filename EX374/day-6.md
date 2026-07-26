#### Defining Variables in practice.
##### Host/Group variables file
To use host or group specific variables, you will need to create a directory in the ansible working directly with the names `host_vars` and `group_vars` respectively. Then inside of this directories, create a variable file that corresponds to the name of host or group, and put the variables insied using the key: value pair notation. Note, these type of variables are automatically picked up by the playbook, and only the keys needs to be added where we want to use the values. 

Example, lets say we have an inventory file with the groups `rhel` and `ubuntu`:
```yaml
[rhel]
vm2

[ubuntu]
vm3
```
We can create this directory:
`<ansible_working_directory>/group_vars`

Then create these files, to have variables specific to managed hosts in either `rhel` or `ubuntu`:

`<ansible_working_directory>/group_vars/rhel.yml`
content below:
```yaml
text: "I am in group rhel"
```
`<ansible_working_directory>/group_vars/ubuntu.yml`
content below:
```yaml
text: "I am in group ubuntu"
```

Also, if we wanted host specific variables, we can create the directory;
`<ansible_working_directory>/host_vars`
Then create a host specific variable inside;
`<ansible_working_directory>/host_vars/vm2.yml`
content below:
```yaml
htext: "I am vm2"
```
`<ansible_working_directory>/host_vars/vm3.yml`
content below:
```yaml
htext: "I am vm3"
```

Once these variables are defined, the playbook below can be used to test them, note we don't have to import the variables are the top of the play, the playbook automatically detects them due to the directory structure. 

```yaml
---
- name: Testing host and group vars files
  hosts: all
  tasks:
  - name: Testing group variables
    debug:
      msg: "{{ text }}"
  - name: Testing host variables
    debug:
      msg: "{{ htext }}"
```

Result of running the playbook:

```shell
PLAY [Testing host and group vars files] *********************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm3]
ok: [vm2]

TASK [Testing group variables] *******************************************************************************************************************************************************************************
ok: [vm2] => {
    "msg": "I am in group rhel"
}
ok: [vm3] => {
    "msg": " I am in group ubuntu"
}

TASK [Testing host variables] ********************************************************************************************************************************************************************************
ok: [vm2] => {
    "msg": "I am vm2"
}
ok: [vm3] => {
    "msg": "I am vm3"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm2                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm3                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
#### Ansible Facts

Ansible facts are variables that are automatically set and discovered by ansible on the managed hosts.
These include information such as;
- Available disk space
- memory
- hostname
- IP Address 
and much more. 

Use the adhoc command below to get the facts on a host;
`ansible vm2 -m setup | less`

##### Result of ansible facts from the `setup` module.
<details>
<summary> Click to expand </summary>

```yaml
vm2 | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "192.168.109.159"
        ],
        "ansible_all_ipv6_addresses": [
            "fe80::20c:29ff:fe83:98d6"
        ],
        "ansible_apparmor": {
            "status": "disabled"
        },
        "ansible_architecture": "x86_64",
        "ansible_bios_date": "11/12/2020",
        "ansible_bios_vendor": "Phoenix Technologies LTD",
        "ansible_bios_version": "6.00",
        "ansible_board_asset_tag": "NA",
        "ansible_board_name": "440BX Desktop Reference Platform",
        "ansible_board_serial": "None",
        "ansible_board_vendor": "Intel Corporation",
        "ansible_board_version": "None",
        "ansible_chassis_asset_tag": "No Asset Tag",
        "ansible_chassis_serial": "None",
        "ansible_chassis_vendor": "No Enclosure",
        "ansible_chassis_version": "N/A",
        "ansible_cmdline": {
            "BOOT_IMAGE": "(hd0,msdos1)/vmlinuz-5.14.0-687.26.1.el9_8.x86_64",
            "crashkernel": "1G-2G:192M,2G-64G:256M,64G-:512M",
            "rd.lvm.lv": "rhel/swap",
            "resume": "/dev/mapper/rhel-swap",
            "ro": true,
            "root": "/dev/mapper/rhel-root"
        },
        "ansible_date_time": {
            "date": "2026-07-26",
            "day": "26",
            "epoch": "1785101187",
            "epoch_int": "1785101187",
            "hour": "14",
            "iso8601": "2026-07-26T21:26:27Z",
            "iso8601_basic": "20260726T142627418629",
            "iso8601_basic_short": "20260726T142627",
            "iso8601_micro": "2026-07-26T21:26:27.418629Z",
            "minute": "26",
            "month": "07",
            "second": "27",
            "time": "14:26:27",
            "tz": "MST",
            "tz_dst": "MST",
            "tz_offset": "-0700",
            "weekday": "Sunday",
            "weekday_number": "0",
            "weeknumber": "29",
            "year": "2026"
        },
        "ansible_default_ipv4": {
            "address": "192.168.109.159",
            "alias": "ens160",
            "broadcast": "192.168.109.255",
            "gateway": "192.168.109.2",
            "interface": "ens160",
            "macaddress": "00:0c:29:83:98:d6",
            "mtu": 1500,
            "netmask": "255.255.255.0",
            "network": "192.168.109.0",
            "prefix": "24",
            "type": "ether"
        },
        "ansible_default_ipv6": {},
        "ansible_device_links": {
            "ids": {
                "dm-0": [
                    "dm-name-rhel-root",
                    "dm-uuid-LVM-hwAS0cMbYiAn7ke37e60c5rQJhWv3LCZHKKIgBYvvJ3R3Il2rzhpk9PLdCBRpciI"
                ],
                "dm-1": [
                    "dm-name-rhel-swap",
                    "dm-uuid-LVM-hwAS0cMbYiAn7ke37e60c5rQJhWv3LCZ7d1290XZkMKDPyeuI3tZrGfb02GHvqax"
                ],
                "nvme0n1": [
                    "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000",
                    "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000_1",
                    "nvme-eui.ec8cb69ca7f7d54d000c296042abb0e9"
                ],
                "nvme0n1p1": [
                    "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000-part1",
                    "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000_1-part1",
                    "nvme-eui.ec8cb69ca7f7d54d000c296042abb0e9-part1"
                ],
                "nvme0n1p2": [
                    "lvm-pv-uuid-JXbXRa-XaJw-9Vq4-JQXH-Wceu-2f6N-FKvdhC",
                    "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000-part2",
                    "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000_1-part2",
                    "nvme-eui.ec8cb69ca7f7d54d000c296042abb0e9-part2"
                ],
                "sr0": [
                    "ata-VMware_Virtual_SATA_CDRW_Drive_00000000000000000001"
                ],
                "sr1": [
                    "ata-VMware_Virtual_SATA_CDRW_Drive_01000000000000000001"
                ]
            },
            "labels": {},
            "masters": {
                "nvme0n1p2": [
                    "dm-0",
                    "dm-1"
                ]
            },
            "uuids": {
                "dm-0": [
                    "9bdcc6ed-294c-4ce1-9bf0-5b7a07288c34"
                ],
                "dm-1": [
                    "b59ca2ee-2f39-48b8-8bf0-b616ac80b35b"
                ],
                "nvme0n1p1": [
                    "70563f04-ef98-4ae3-ae4d-6c625810026c"
                ]
            }
        },
        "ansible_devices": {
            "dm-0": {
                "holders": [],
                "host": "",
                "links": {
                    "ids": [
                        "dm-name-rhel-root",
                        "dm-uuid-LVM-hwAS0cMbYiAn7ke37e60c5rQJhWv3LCZHKKIgBYvvJ3R3Il2rzhpk9PLdCBRpciI"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": [
                        "9bdcc6ed-294c-4ce1-9bf0-5b7a07288c34"
                    ]
                },
                "model": null,
                "partitions": {},
                "removable": "0",
                "rotational": "0",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "",
                "sectors": "94519296",
                "sectorsize": "512",
                "size": "45.07 GB",
                "support_discard": "512",
                "vendor": null,
                "virtual": 1
            },
            "dm-1": {
                "holders": [],
                "host": "",
                "links": {
                    "ids": [
                        "dm-name-rhel-swap",
                        "dm-uuid-LVM-hwAS0cMbYiAn7ke37e60c5rQJhWv3LCZ7d1290XZkMKDPyeuI3tZrGfb02GHvqax"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": [
                        "b59ca2ee-2f39-48b8-8bf0-b616ac80b35b"
                    ]
                },
                "model": null,
                "partitions": {},
                "removable": "0",
                "rotational": "0",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "",
                "sectors": "8232960",
                "sectorsize": "512",
                "size": "3.93 GB",
                "support_discard": "512",
                "vendor": null,
                "virtual": 1
            },
            "nvme0n1": {
                "holders": [],
                "host": "Non-Volatile memory controller: VMware NVMe SSD Controller",
                "links": {
                    "ids": [
                        "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000",
                        "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000_1",
                        "nvme-eui.ec8cb69ca7f7d54d000c296042abb0e9"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": []
                },
                "model": "VMware Virtual NVMe Disk",
                "partitions": {
                    "nvme0n1p1": {
                        "holders": [],
                        "links": {
                            "ids": [
                                "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000-part1",
                                "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000_1-part1",
                                "nvme-eui.ec8cb69ca7f7d54d000c296042abb0e9-part1"
                            ],
                            "labels": [],
                            "masters": [],
                            "uuids": [
                                "70563f04-ef98-4ae3-ae4d-6c625810026c"
                            ]
                        },
                        "sectors": "2097152",
                        "sectorsize": 512,
                        "size": "1.00 GB",
                        "start": "2048",
                        "uuid": "70563f04-ef98-4ae3-ae4d-6c625810026c"
                    },
                    "nvme0n1p2": {
                        "holders": [
                            "rhel-swap",
                            "rhel-root"
                        ],
                        "links": {
                            "ids": [
                                "lvm-pv-uuid-JXbXRa-XaJw-9Vq4-JQXH-Wceu-2f6N-FKvdhC",
                                "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000-part2",
                                "nvme-VMware_Virtual_NVMe_Disk_VMware_NVME_0000_1-part2",
                                "nvme-eui.ec8cb69ca7f7d54d000c296042abb0e9-part2"
                            ],
                            "labels": [],
                            "masters": [
                                "dm-0",
                                "dm-1"
                            ],
                            "uuids": []
                        },
                        "sectors": "102758400",
                        "sectorsize": 512,
                        "size": "49.00 GB",
                        "start": "2099200",
                        "uuid": null
                    }
                },
                "removable": "0",
                "rotational": "0",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "none",
                "sectors": "104857600",
                "sectorsize": "512",
                "serial": "VMware",
                "size": "50.00 GB",
                "support_discard": "512",
                "vendor": null,
                "virtual": 1
            },
            "sr0": {
                "holders": [],
                "host": "SATA controller: VMware SATA AHCI controller",
                "links": {
                    "ids": [
                        "ata-VMware_Virtual_SATA_CDRW_Drive_00000000000000000001"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": []
                },
                "model": "VMware SATA CD00",
                "partitions": {},
                "removable": "1",
                "rotational": "0",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "mq-deadline",
                "sectors": "2097151",
                "sectorsize": "512",
                "size": "1024.00 MB",
                "support_discard": "512",
                "vendor": "NECVMWar",
                "virtual": 1
            },
            "sr1": {
                "holders": [],
                "host": "SATA controller: VMware SATA AHCI controller",
                "links": {
                    "ids": [
                        "ata-VMware_Virtual_SATA_CDRW_Drive_01000000000000000001"
                    ],
                    "labels": [],
                    "masters": [],
                    "uuids": []
                },
                "model": "VMware SATA CD01",
                "partitions": {},
                "removable": "1",
                "rotational": "0",
                "sas_address": null,
                "sas_device_handle": null,
                "scheduler_mode": "mq-deadline",
                "sectors": "2097151",
                "sectorsize": "512",
                "size": "1024.00 MB",
                "support_discard": "512",
                "vendor": "NECVMWar",
                "virtual": 1
            }
        },
        "ansible_distribution": "RedHat",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_search_string": "Red Hat",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "9",
        "ansible_distribution_release": "Plow",
        "ansible_distribution_version": "9.8",
        "ansible_dns": {
            "nameservers": [
                "8.8.8.8"
            ],
            "search": [
                "cezeh.lab"
            ]
        },
        "ansible_domain": "cezeh.lab",
        "ansible_effective_group_id": 0,
        "ansible_effective_user_id": 0,
        "ansible_ens160": {
            "active": true,
            "device": "ens160",
            "features": {
                "esp_hw_offload": "off [fixed]",
                "esp_tx_csum_hw_offload": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on",
                "hsr_dup_offload": "off [fixed]",
                "hsr_fwd_offload": "off [fixed]",
                "hsr_tag_ins_offload": "off [fixed]",
                "hsr_tag_rm_offload": "off [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "on",
                "loopback": "off [fixed]",
                "macsec_hw_offload": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "on",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_gro_list": "off",
                "rx_udp_gro_forwarding": "off",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "on [fixed]",
                "rx_vlan_offload": "on",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tls_hw_record": "off [fixed]",
                "tls_hw_rx_offload": "off [fixed]",
                "tls_hw_tx_offload": "off [fixed]",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "off [fixed]",
                "tx_checksumming": "on",
                "tx_esp_segmentation": "off [fixed]",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_list": "off [fixed]",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipxip4_segmentation": "off [fixed]",
                "tx_ipxip6_segmentation": "off [fixed]",
                "tx_nocache_copy": "off",
                "tx_scatter_gather": "on",
                "tx_scatter_gather_fraglist": "off [fixed]",
                "tx_sctp_segmentation": "off [fixed]",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "off [fixed]",
                "tx_tcp_mangleid_segmentation": "off",
                "tx_tcp_segmentation": "on",
                "tx_tunnel_remcsum_segmentation": "off [fixed]",
                "tx_udp_segmentation": "off [fixed]",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "on",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "vlan_challenged": "off [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv4": {
                "address": "192.168.109.159",
                "broadcast": "192.168.109.255",
                "netmask": "255.255.255.0",
                "network": "192.168.109.0",
                "prefix": "24"
            },
            "ipv6": [
                {
                    "address": "fe80::20c:29ff:fe83:98d6",
                    "prefix": "64",
                    "scope": "link"
                }
            ],
            "macaddress": "00:0c:29:83:98:d6",
            "module": "vmxnet3",
            "mtu": 1500,
            "pciid": "0000:03:00.0",
            "promisc": false,
            "speed": 10000,
            "timestamping": [],
            "type": "ether"
        },
        "ansible_env": {
            "HOME": "/root",
            "LANG": "en_US.UTF-8",
            "LOGNAME": "root",
            "LS_COLORS": "rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=01;37;41:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.webp=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=01;36:*.au=01;36:*.flac=01;36:*.m4a=01;36:*.mid=01;36:*.midi=01;36:*.mka=01;36:*.mp3=01;36:*.mpc=01;36:*.ogg=01;36:*.ra=01;36:*.wav=01;36:*.oga=01;36:*.opus=01;36:*.spx=01;36:*.xspf=01;36:",
            "MAIL": "/var/mail/root",
            "PATH": "/sbin:/bin:/usr/sbin:/usr/bin",
            "PWD": "/home/admuser",
            "SHELL": "/bin/bash",
            "SHLVL": "0",
            "SUDO_COMMAND": "/bin/sh -c echo BECOME-SUCCESS-uavekbndzxbgxafhxippmxrtvapfaljd ; /usr/bin/python3 /home/admuser/.ansible/tmp/ansible-tmp-1785101186.4577973-4962-31292739417010/AnsiballZ_setup.py",
            "SUDO_GID": "1000",
            "SUDO_HOME": "/home/admuser",
            "SUDO_TTY": "/dev/pts/0",
            "SUDO_UID": "1000",
            "SUDO_USER": "admuser",
            "TERM": "xterm",
            "USER": "root",
            "_": "/usr/bin/python3"
        },
        "ansible_fibre_channel_wwn": [],
        "ansible_fips": false,
        "ansible_form_factor": "Other",
        "ansible_fqdn": "vm2.cezeh.lab",
        "ansible_hostname": "vm2",
        "ansible_hostnqn": "nqn.2014-08.org.nvmexpress:uuid:8ce34d56-45c1-604e-b16e-1b9993a67c9e",
        "ansible_interfaces": [
            "ens160",
            "lo"
        ],
        "ansible_is_chroot": false,
        "ansible_iscsi_iqn": "",
        "ansible_kernel": "5.14.0-687.26.1.el9_8.x86_64",
        "ansible_kernel_version": "#1 SMP PREEMPT_DYNAMIC Mon Jul 13 13:49:10 EDT 2026",
        "ansible_lo": {
            "active": true,
            "device": "lo",
            "features": {
                "esp_hw_offload": "off [fixed]",
                "esp_tx_csum_hw_offload": "off [fixed]",
                "generic_receive_offload": "on",
                "generic_segmentation_offload": "on",
                "highdma": "on [fixed]",
                "hsr_dup_offload": "off [fixed]",
                "hsr_fwd_offload": "off [fixed]",
                "hsr_tag_ins_offload": "off [fixed]",
                "hsr_tag_rm_offload": "off [fixed]",
                "hw_tc_offload": "off [fixed]",
                "l2_fwd_offload": "off [fixed]",
                "large_receive_offload": "off [fixed]",
                "loopback": "on [fixed]",
                "macsec_hw_offload": "off [fixed]",
                "ntuple_filters": "off [fixed]",
                "receive_hashing": "off [fixed]",
                "rx_all": "off [fixed]",
                "rx_checksumming": "on [fixed]",
                "rx_fcs": "off [fixed]",
                "rx_gro_hw": "off [fixed]",
                "rx_gro_list": "off",
                "rx_udp_gro_forwarding": "off",
                "rx_udp_tunnel_port_offload": "off [fixed]",
                "rx_vlan_filter": "off [fixed]",
                "rx_vlan_offload": "off [fixed]",
                "rx_vlan_stag_filter": "off [fixed]",
                "rx_vlan_stag_hw_parse": "off [fixed]",
                "scatter_gather": "on",
                "tcp_segmentation_offload": "on",
                "tls_hw_record": "off [fixed]",
                "tls_hw_rx_offload": "off [fixed]",
                "tls_hw_tx_offload": "off [fixed]",
                "tx_checksum_fcoe_crc": "off [fixed]",
                "tx_checksum_ip_generic": "on [fixed]",
                "tx_checksum_ipv4": "off [fixed]",
                "tx_checksum_ipv6": "off [fixed]",
                "tx_checksum_sctp": "on [fixed]",
                "tx_checksumming": "on",
                "tx_esp_segmentation": "off [fixed]",
                "tx_fcoe_segmentation": "off [fixed]",
                "tx_gre_csum_segmentation": "off [fixed]",
                "tx_gre_segmentation": "off [fixed]",
                "tx_gso_list": "on",
                "tx_gso_partial": "off [fixed]",
                "tx_gso_robust": "off [fixed]",
                "tx_ipxip4_segmentation": "off [fixed]",
                "tx_ipxip6_segmentation": "off [fixed]",
                "tx_nocache_copy": "off [fixed]",
                "tx_scatter_gather": "on [fixed]",
                "tx_scatter_gather_fraglist": "on [fixed]",
                "tx_sctp_segmentation": "on",
                "tx_tcp6_segmentation": "on",
                "tx_tcp_ecn_segmentation": "on",
                "tx_tcp_mangleid_segmentation": "on",
                "tx_tcp_segmentation": "on",
                "tx_tunnel_remcsum_segmentation": "off [fixed]",
                "tx_udp_segmentation": "on",
                "tx_udp_tnl_csum_segmentation": "off [fixed]",
                "tx_udp_tnl_segmentation": "off [fixed]",
                "tx_vlan_offload": "off [fixed]",
                "tx_vlan_stag_hw_insert": "off [fixed]",
                "vlan_challenged": "on [fixed]"
            },
            "hw_timestamp_filters": [],
            "ipv4": {
                "address": "127.0.0.1",
                "broadcast": "",
                "netmask": "255.0.0.0",
                "network": "127.0.0.0",
                "prefix": "8"
            },
            "ipv6": [
                {
                    "address": "::1",
                    "prefix": "128",
                    "scope": "host"
                }
            ],
            "mtu": 65536,
            "promisc": false,
            "timestamping": [],
            "type": "loopback"
        },
        "ansible_loadavg": {
            "15m": 0.0,
            "1m": 0.05,
            "5m": 0.01
        },
        "ansible_local": {},
        "ansible_locally_reachable_ips": {
            "ipv4": [
                "127.0.0.0/8",
                "127.0.0.1",
                "192.168.109.159"
            ],
            "ipv6": [
                "::1",
                "fe80::20c:29ff:fe83:98d6"
            ]
        },
        "ansible_lsb": {},
        "ansible_lvm": {
            "lvs": {
                "root": {
                    "size_g": "45.07",
                    "vg": "rhel"
                },
                "swap": {
                    "size_g": "3.93",
                    "vg": "rhel"
                }
            },
            "pvs": {
                "/dev/nvme0n1p2": {
                    "free_g": "0",
                    "size_g": "49.00",
                    "vg": "rhel"
                }
            },
            "vgs": {
                "rhel": {
                    "free_g": "0",
                    "num_lvs": "2",
                    "num_pvs": "1",
                    "size_g": "49.00"
                }
            }
        },
        "ansible_machine": "x86_64",
        "ansible_machine_id": "aeb5a50d2d45473b92a18065c24b1254",
        "ansible_memfree_mb": 2956,
        "ansible_memory_mb": {
            "nocache": {
                "free": 3278,
                "used": 344
            },
            "real": {
                "free": 2956,
                "total": 3622,
                "used": 666
            },
            "swap": {
                "cached": 0,
                "free": 4019,
                "total": 4019,
                "used": 0
            }
        },
        "ansible_memtotal_mb": 3622,
        "ansible_mounts": [
            {
                "block_available": 11173344,
                "block_size": 4096,
                "block_total": 11809143,
                "block_used": 635799,
                "device": "/dev/mapper/rhel-root",
                "fstype": "xfs",
                "inode_available": 23586160,
                "inode_total": 23629824,
                "inode_used": 43664,
                "mount": "/",
                "options": "rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota",
                "size_available": 45766017024,
                "size_total": 48370249728,
                "uuid": "9bdcc6ed-294c-4ce1-9bf0-5b7a07288c34"
            },
            {
                "block_available": 173398,
                "block_size": 4096,
                "block_total": 259584,
                "block_used": 86186,
                "device": "/dev/nvme0n1p1",
                "fstype": "xfs",
                "inode_available": 523922,
                "inode_total": 524288,
                "inode_used": 366,
                "mount": "/boot",
                "options": "rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota",
                "size_available": 710238208,
                "size_total": 1063256064,
                "uuid": "70563f04-ef98-4ae3-ae4d-6c625810026c"
            }
        ],
        "ansible_nodename": "vm2.cezeh.lab",
        "ansible_os_family": "RedHat",
        "ansible_pkg_mgr": "dnf",
        "ansible_proc_cmdline": {
            "BOOT_IMAGE": "(hd0,msdos1)/vmlinuz-5.14.0-687.26.1.el9_8.x86_64",
            "crashkernel": "1G-2G:192M,2G-64G:256M,64G-:512M",
            "rd.lvm.lv": [
                "rhel/root",
                "rhel/swap"
            ],
            "resume": "/dev/mapper/rhel-swap",
            "ro": true,
            "root": "/dev/mapper/rhel-root"
        },
        "ansible_processor": [
            "0",
            "AuthenticAMD",
            "AMD Ryzen 9 5900HS with Radeon Graphics",
            "1",
            "AuthenticAMD",
            "AMD Ryzen 9 5900HS with Radeon Graphics"
        ],
        "ansible_processor_cores": 1,
        "ansible_processor_count": 2,
        "ansible_processor_nproc": 2,
        "ansible_processor_threads_per_core": 1,
        "ansible_processor_vcpus": 2,
        "ansible_product_name": "VMware Virtual Platform",
        "ansible_product_serial": "VMware-56 4d 31 54 59 ef 70 e0-e6 4b 63 76 b9 83 98 d6",
        "ansible_product_uuid": "54314d56-ef59-e070-e64b-6376b98398d6",
        "ansible_product_version": "None",
        "ansible_python": {
            "executable": "/usr/bin/python3",
            "has_sslcontext": true,
            "type": "cpython",
            "version": {
                "major": 3,
                "micro": 25,
                "minor": 9,
                "releaselevel": "final",
                "serial": 0
            },
            "version_info": [
                3,
                9,
                25,
                "final",
                0
            ]
        },
        "ansible_python_version": "3.9.25",
        "ansible_real_group_id": 0,
        "ansible_real_user_id": 0,
        "ansible_selinux": {
            "config_mode": "permissive",
            "mode": "permissive",
            "policyvers": 33,
            "status": "enabled",
            "type": "targeted"
        },
        "ansible_selinux_python_present": true,
        "ansible_service_mgr": "systemd",
        "ansible_swapfree_mb": 4019,
        "ansible_swaptotal_mb": 4019,
        "ansible_system": "Linux",
        "ansible_system_capabilities": [],
        "ansible_system_capabilities_enforced": "False",
        "ansible_system_vendor": "VMware, Inc.",
        "ansible_uptime_seconds": 4528,
        "ansible_user_dir": "/root",
        "ansible_user_gecos": "root",
        "ansible_user_gid": 0,
        "ansible_user_id": "root",
        "ansible_user_shell": "/bin/bash",
        "ansible_user_uid": 0,
        "ansible_userspace_architecture": "x86_64",
        "ansible_userspace_bits": "64",
        "ansible_virtualization_role": "guest",
        "ansible_virtualization_tech_guest": [
            "VMware"
        ],
        "ansible_virtualization_tech_host": [],
        "ansible_virtualization_type": "VMware",
        "discovered_interpreter_python": "/usr/bin/python3",
        "gather_subset": [
            "all"
        ],
        "module_setup": true
    },
    "changed": false
}
```
</details>