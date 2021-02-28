# How to run T2 test cases on a VoQ System

- [How to run T2 test cases on a VoQ System](#how-to-run-t2-test-cases-on-a-voq-system)
  * [Overview](#overview)
  * [T2 Test Coverage](#t2-test-coverage)
  * [T2 topology](#t2-topology)
  * [T2 Testbed Setup](#t2-testbed-setup)
    + [Checklist to add T2 topology to your lab](#checklist-to-add-t2-topology-to-your-lab)
    + [Linux networking and cEOS bringup](#linux-networking-and-ceos-bringup)
    + [Generation of minigraph.xml for all DUTs](#generation-of-minigraphxml-for-all-duts)
    + [Deploying minigraph.xml](#deploying-minigraphxml)
  * [Tests execution](#tests-execution)
  * [Work pending by the Subgroup](#work-pending-by-the-subgroup)

## Overview
This document explains how to run the currently support T2 test cases on a VoQ System.


## T2 Test Coverage
The feature set for a VoQ System are expected to be executed on a T2 topology.

T2 feature set is assumed to be the same as T1. Existing T1 test cases have been converted to multi-dut by the OC and this is an ongoing effort. This progress is tracked in the [Chassis Subgroup](https://github.com/Azure/SONiC/wiki/SONiC-Chassis-Subgroup) tracker. The PRs which introduce new test cases that are applicable only to T2 topology are also mentioned in the [Chassis Subgroup](https://github.com/Azure/SONiC/wiki/SONiC-Chassis-Subgroup) Tracker.

## T2 topology

The T2 topology is now a [standardized topology](https://github.com/Azure/sonic-mgmt/blob/master/docs/testbed/README.testbed.Topology.md) in the OC.

![T2 Topology](https://github.com/Azure/sonic-mgmt/blob/master/docs/testbed/img/testbed-t2.png)

The HW requirements are as follows:
-   A standard Ubuntu Server that will run 72 cEOS dockers, sonic-mgmt docker and the PTF
-   A DUT with 3 line cards and a supervisor card.
        -  32 ports on each card connected to a fanout
- A fanout with 96 ports

The above is the topology proposed for a VoQ system where each line card has 1 ASIC. When new HWSKUs support multiple ASIC on a single line card, a variant of this topology can be introduced to cover the same set of features.

Once the test coverage for T2 is complete, the OC can review the current T2 topology to reduce the line card and fanout requirements further, if required.

## T2 Testbed Setup

### Checklist to add T2 topology to your lab

 1.Edit your testbed.yaml to add this new setup. The 3 line cards and the supervisor card are placed in a list. Our entry, for example, is as follows

    - conf-name: ixr_vdk_chassis4-t2
      group-name: vms_12
      topo: t2
      ptf_image_name: docker-ptf
      ptf: ptf_vms_12
      ptf_ip: 10.250.12.188/24
      ptf_ipv6:
      server: server_12
      vm_base: VM1200
      dut: [ixr-vdk-board7, ixr-vdk-board4, ixr-vdk-board3, ixr-cpm2-chassis4]
      comment: ' T2 OC Tests vdk_chassis4'

 2.  Edit your veos.vtb to add the 72 VMs as mentioned in the T2 topology. Ours is given below

    all:
      children:
        vm_host:
          children:
            vm_host_12:
    
    eos:
      children:
        vms_12:
    
    servers:
      vars:
        topologies:
          - t2
    
      children:
        server_12:
    
    ptf:
      hosts:
        ptf_vms_12:
          ansible_host: 10.250.12.188
          ansible_hostv6: fec0::ffff:afa:c
      vars:
          ansible_user: root
          ansible_password: root
    sonic:
      vars:
        mgmt_subnet_mask_length: 24
    
    server_12:
      vars:
        host_var_file: host_vars/sonic-tb12.yml
      children:
        vm_host_12:
        vms_12:
    
    vm_host_12:
      hosts:
        sonic-tb12:
          ansible_host: 152.148.150.62
          ansible_user: ansible
          ansible_password: 123
          ansible_become_password: 123
    
    vms_12:
      hosts:
        VM1200:
          ansible_host: 10.250.12.50
          ansible_user: admin
          ansible_password: 123456
        VM1201:
          ansible_host: 10.250.12.51
          ansible_user: admin
          ansible_password: 123456
        VM1202:
          ansible_host: 10.250.12.52
          ansible_user: admin
          ansible_password: 123456
        VM1203:
          ansible_host: 10.250.12.53
          ansible_user: admin
          ansible_password: 123456
        VM1204:
          ansible_host: 10.250.12.54
          ansible_user: admin
          ansible_password: 123456
        VM1205:
          ansible_host: 10.250.12.55
          ansible_user: admin
          ansible_password: 123456
        VM1206:
          ansible_host: 10.250.12.56
          ansible_user: admin
          ansible_password: 123456
        VM1207:
          ansible_host: 10.250.12.57
          ansi7le_user: admin
          ansible_password: 123456
        VM1208:
          ansible_host: 10.250.12.58
          ansible_user: admin
          ansible_password: 123456
        VM1209:
          ansible_host: 10.250.12.59
          ansible_user: admin
          ansible_password: 123456
        VM1210:
          ansible_host: 10.250.12.60
          ansible_user: admin
          ansible_password: 123456
        VM1211:
          ansible_host: 10.250.12.61
          ansible_user: admin
          ansible_password: 123456
        VM1212:
          ansible_host: 10.250.12.62
          ansible_user: admin
          ansible_password: 123456
        VM1213:
          ansible_host: 10.250.12.63
          ansible_user: admin
          ansible_password: 123456
        VM1214:
          ansible_host: 10.250.12.64
          ansible_user: admin
          ansible_password: 123456
        VM1215:
          ansible_host: 10.250.12.65
          ansible_user: admin
          ansible_password: 123456
        VM1216:
          ansible_host: 10.250.12.66
          ansible_user: admin
          ansible_password: 123456
        VM1217:
          ansible_host: 10.250.12.67
          ansible_user: admin
          ansible_password: 123456
        VM1218:
          ansible_host: 10.250.12.68
          ansible_user: admin
          ansible_password: 123456
        VM1219:
          ansible_host: 10.250.12.69
          ansible_user: admin
          ansible_password: 123456
        VM1220:
          ansible_host: 10.250.12.70
          ansible_user: admin
          ansible_password: 123456
        VM1221:
          ansible_host: 10.250.12.71
          ansible_user: admin
          ansible_password: 123456
        VM1222:
          ansible_host: 10.250.12.72
          ansible_user: admin
          ansible_password: 123456
        VM1223:
          ansible_host: 10.250.12.73
          ansible_user: admin
          ansible_password: 123456
        VM1224:
          ansible_host: 10.250.12.74
          ansible_user: admin
          ansible_password: 123456
        VM1225:
          ansible_host: 10.250.12.75
          ansible_user: admin
          ansible_password: 123456
        VM1226:
          ansible_host: 10.250.12.76
          ansible_user: admin
          ansible_password: 123456
        VM1227:
          ansible_host: 10.250.12.77
          ansible_user: admin
          ansible_password: 123456
        VM1228:
          ansible_host: 10.250.12.78
          ansible_user: admin
          ansible_password: 123456
        VM1229:
          ansible_host: 10.250.12.79
          ansible_user: admin
          ansible_password: 123456
        VM1230:
          ansible_host: 10.250.12.80
          ansible_user: admin
          ansible_password: 123456
        VM1231:
          ansible_host: 10.250.12.81
          ansible_user: admin
          ansible_password: 123456
        VM1232:
          ansible_host: 10.250.12.82
          ansible_user: admin
          ansible_password: 123456
        VM1233:
          ansible_host: 10.250.12.83
          ansible_user: admin
          ansible_password: 123456
        VM1234:
          ansible_host: 10.250.12.84
          ansible_user: admin
          ansible_password: 123456
        VM1235:
          ansible_host: 10.250.12.85
          ansible_user: admin
          ansible_password: 123456
        VM1236:
          ansible_host: 10.250.12.86
          ansible_user: admin
          ansible_password: 123456
        VM1237:
          ansible_host: 10.250.12.87
          ansible_user: admin
          ansible_password: 123456
        VM1238:
          ansible_host: 10.250.12.88
          ansible_user: admin
          ansible_password: 123456
        VM1239:
          ansible_host: 10.250.12.89
          ansible_user: admin
          ansible_password: 123456
        VM1240:
          ansible_host: 10.250.12.90
          ansible_user: admin
          ansible_password: 123456
        VM1241:
          ansible_host: 10.250.12.91
          ansible_user: admin
          ansible_password: 123456
        VM1242:
          ansible_host: 10.250.12.92
          ansible_user: admin
          ansible_password: 123456
        VM1243:
          ansible_host: 10.250.12.93
          ansible_user: admin
          ansible_password: 123456
        VM1244:
          ansible_host: 10.250.12.94
          ansible_user: admin
          ansible_password: 123456
        VM1245:
          ansible_host: 10.250.12.95
          ansible_user: admin
          ansible_password: 123456
        VM1246:
          ansible_host: 10.250.12.96
          ansible_user: admin
          ansible_password: 123456
        VM1247:
          ansible_host: 10.250.12.97
          ansible_user: admin
          ansible_password: 123456
        VM1248:
          ansible_host: 10.250.12.98
          ansible_user: admin
          ansible_password: 123456
        VM1249:
          ansible_host: 10.250.12.99
          ansible_user: admin
          ansible_password: 123456
        VM1250:
          ansible_host: 10.250.12.100
          ansible_user: admin
          ansible_password: 123456
        VM1251:
          ansible_host: 10.250.12.101
          ansible_user: admin
          ansible_password: 123456
        VM1252:
          ansible_host: 10.250.12.102
          ansible_user: admin
          ansible_password: 123456
        VM1253:
          ansible_host: 10.250.12.103
          ansible_user: admin
          ansible_password: 123456
        VM1254:
          ansible_host: 10.250.12.104
          ansible_user: admin
          ansible_password: 123456
        VM1255:
          ansible_host: 10.250.12.105
          ansible_user: admin
          ansible_password: 123456
        VM1256:
          ansible_host: 10.250.12.106
          ansible_user: admin
          ansible_password: 123456
        VM1257:
          ansible_host: 10.250.12.107
          ansible_user: admin
          ansible_password: 123456
        VM1258:
          ansible_host: 10.250.12.108
          ansible_user: admin
          ansible_password: 123456
        VM1259:
          ansible_host: 10.250.12.109
          ansible_user: admin
          ansible_password: 123456
        VM1260:
          ansible_host: 10.250.12.110
          ansible_user: admin
          ansible_password: 123456
        VM1261:
          ansible_host: 10.250.12.111
          ansible_user: admin
          ansible_password: 123456
        VM1262:
          ansible_host: 10.250.12.112
          ansible_user: admin
          ansible_password: 123456
        VM1263:
          ansible_host: 10.250.12.113
          ansible_user: admin
          ansible_password: 123456
        VM1264:
          ansible_host: 10.250.12.114
          ansible_user: admin
          ansible_password: 123456
        VM1265:
          ansible_host: 10.250.12.115
          ansible_user: admin
          ansible_password: 123456
        VM1266:
          ansible_host: 10.250.12.116
          ansible_user: admin
          ansible_password: 123456
        VM1267:
          ansible_host: 10.250.12.117
          ansible_user: admin
          ansible_password: 123456
        VM1268:
          ansible_host: 10.250.12.118
          ansible_user: admin
          ansible_password: 123456
        VM1269:
          ansible_host: 10.250.12.119
          ansible_user: admin
          ansible_password: 123456
        VM1270:
          ansible_host: 10.250.12.120
          ansible_user: admin
          ansible_password: 123456
        VM1271:
          ansible_host: 10.250.12.121
          ansible_user: admin
          ansible_password: 123456
        VM1272:
          ansible_host: 10.250.12.122
          ansible_user: admin
          ansible_password: 123456
   
 4. The PTF and fanout section of the inventory YAML file will be the same format as in a T1 test bed. Note that there is only 1 PTF host in the entire topology.

 5. The details of the chassis and its components that are to be included in the inventory YAML file are given below. Our inventory file below assumes that the chassis is populated with:

         - 3 line cards at slots 0, 6 and 7
         - 1 fabric cards at slot 4
         - 2 PSUs in slots 7 and 8
         - a supervisor card in slot A
        
Replace the model, serial, base_mac, ansible_host, and syseeprom_info of each card as per your lab setup and HW details. Also, for the supervisor card, add skip_modules for linecards,fabric cards, and psus missing from the chassis.

    all:
      children:
        lab:
          vars:
            sonic_version: v2
            asic_type: broadcom
            ansible_ssh_pass: 123
            mgmt_subnet_mask_length: 21
          children:
             sonic:
               children:
                 sonic_vodka:
                 sonic_cpm:
             fanout:

    sonic_vodka:
      vars:
        sonic_hwsku: ixr7250-32x100g-4x400g
        hwsku: ixr7250-32x100g-4x400g
        sonic_hw_platform: x86_64-nokia_ixr7250_32x100g_4x400g-r0
        num_asics: 1
      hosts:
        ixr-vdk-board3:
          ansible_host: 152.148.150.148
          model: "N/A"
          serial: NS193710031
          base_mac: 24:21:24:05:81:51
          syseeprom_info:
            '0xFE': '0x80A91EB0'
            '0x23': 'NS193710031'
            '0x21': 'ixr7250-32x100g-4x400g'
            '0x26': '56'
            '0x24': '24:21:24:05:81:51'
            '0x25': '20191001'
            '0x2A': '2'
        ixr-vdk-board4:
          ansible_host: 152.148.150.147
          model: "N/A"
          serial: NS193410069
          base_mac: 14:7B:AC:3A:C9:7F
          syseeprom_info:
            '0xFE': '0xA0D17801'
            '0x23': 'NS193410069'
            '0x21': 'ixr7250-32x100g-4x400g'
            '0x26': '56'
            '0x24': '14:7B:AC:3A:C9:7F'
            '0x25': '20190828'
            '0x2A': '2'
        ixr-vdk-board7:
          ansible_host: 152.148.150.141
          console_ip: 152.148.150.6
          console_port: 2021
          model: 3HE12522AARA01
          serial: NS193410048
          base_mac: 14:7B:AC:3A:C9:55
          syseeprom_info:
            '0xFE': '0x1F187F5E'
            '0x22': '3HE12522AARA01'
            '0x23': 'NS193410048'
            '0x21': 'ixr7250-32x100g-4x400g'
            '0x26': '56'
            '0x24': '14:7B:AC:3A:C9:55'
            '0x25': '20190909'
            '0x2A': '2'
    
    sonic_cpm:
      vars:
        sonic_hwsku: ixr7250-CPM
        hwsku: ixr7250-CPM
        sonic_hw_platform: x86_64-nokia_ixr7250_cpm-r0
        num_asics: 1
        type: supervisor
      hosts:
        ixr-cpm2-chassis4:
          ansible_host: 152.148.150.47
          model: 3HE12458AA0401
          serial: NS193410077
          base_mac: 14:7B:AC:B5:60:2F
          syseeprom_info:
            '0xFE': '0x7B7CD094'
            '0x22': '3HE12458AA0401'
            '0x23': 'NS193410077'
            '0x21': 'ixr7250-CPM'
            '0x26': '56'
            '0x24': '14:7B:AC:B5:60:2F'
            '0x2A': '5'
          skip_modules:
            line-cards:
              - LINE-CARD1
              - LINE-CARD2
              - LINE-CARD3
              - LINE-CARD4
              - LINE-CARD5
            fabric-cards:
              - FABRIC-CARD0
              - FABRIC-CARD1
              - FABRIC-CARD3
              - FABRIC-CARD5
            psus:
              - PSU1
              - PSU2
              - PSU3
              - PSU4
              - PSU5
              - PSU6
              - PSU9
              - PSU10
              - PSU11
              - PSU12

 
### Linux networking and cEOS bringup
This step is no different than what is done in , say, a T1 topology.

    ./testbed-cli.sh -t testbed.csv -m veos -k ceos add-topo ixr-vdk-chassis2-t2 password.txt

### Generation of minigraph.xml for all DUTs

gen-mg does not yet support generation of VoQ-specific metadata. This support has to be added by the Sonic Chassis community. Hence, our recommendation is to use a minigraph.xml file for each DUT without any VoQ related metadata, similar to a pizza box 

    ./testbed-cli.sh -t testbed.csv -m veos gen-mg ixr-vdk-chassis2-t2 lab password.txt -e copy=true -e deploy=false

The above command will generate and copy the minigraph file for all the linecards and supervisor card. It needs to be adjusted to incorporate [PR#2888](https://github.com/Azure/sonic-mgmt/pull/2888) to copy an already generated minigraph file to the DUT.

### Deploying minigraph.xml

Since the generated minigraph file does not contain the VoQ related info, we can't deploy it to generate a config_db that has the VoQ related info. Instead, on each linecard
- run sonic_cfggen to generate a temporary config_db.json.
        
            sudo sonic-cfggen -m /etc/sonic/minigraph.xml --print-data > /home/admin/config_db_oc_mg.json
- Manually add the VoQ related data to the generated config_db_oc_mg.json 
        - VoQ info to DEVICE_METADATA.localhost
                

                "DEVICE_METADATA": {
                "localhost": {
                    .
                    .
                    "switch_type": "voq",
                    "switch_id": "42",
                    "asic_id": "04:00.0",
                    "max_cores": "48",
                    "asic_name": "Asic0"
                }
              },

        - BGP_INTERNAL_NEIGHBOR metadata for iBGP connectivity to other linecards

                  "BGP_INTERNAL_NEIGHBOR": {
                 "3.3.3.1": {
               "asn": "65100",
               "holdtime": "180",
              "keepalive": "60",
              "local_addr": "3.3.3.22",
              "name": "board7_Asic0",
              "nhopself": "0",
              "rrclient": "0"
              },
              "3333::3:1": {
                "asn": "65100",
                "holdtime": "180",
                "keepalive": "60",
                "local_addr": "3333::3:22",
                "name": "board7_Asic0",
                "nhopself": "0",
                "rrclient": "0"
            },
            .
            .
          },
       

        - SYSTEM_PORT info for each linecard's frontpanel port, recycle port, and Inband port derived from the jr2 BCM file
                

                  

                  {
                    "SYSTEM_PORT": {
                        "Slot7|Asic0|Ethernet12": {
                            "system_port_id": "1165",
                            "speed": "400000",
                            "switch_id": "36",
                            "core_index": "0",
                            "core_port_index": "13"
                        },
                        .
                        .
                        "Slot7|Asic0|Asic0": {
                            "system_port_id": "1152",
                            "speed": "400000",
                            "switch_id": "36",
                            "core_index": "0",
                            "core_port_index": "0"
                        },
                       "Slot7|Asic0|Ethernet-IB0": {
                            "system_port_id": "1189",
                            "speed": "400000",
                            "switch_id": "36",
                            "core_index": "1",
                            "core_port_index": "37"
                        },
                        .
                        .
                      }  


- Copy the modified config_db.json to /etc/sonic/config_db.json on all the linecards.
- Reboot the supervisor and all the linecards.
- Wait for SONiC to stabilize and ports to come up (~10 minutes)
  
## Tests execution
For now, instead of having a 't2' topology marker, we use t2_tests.sh similar to kvmtest.sh to create a list of applicable tests and call run_tests.sh:

    echo "log_path = $log_path"
    echo "inventory = $inventory"
    tbname=$1
    dut=$2
    RUNTEST_CLI_COMMON_OPTS="\
    -i $inventory \
    -d $dut \
    -n $tbname \
    -f $testbed_file \
    -k debug \
    -a False \
    -O \
    -r"
    .
    .
    tests="\
        arp/test_arpall.py \
        arp/test_neighbor_mac_noptf.py \
        test_interfaces.py \
        test_nbr_health.py \
        test_features.py \
        syslog \
        route/test_default_route.py \
        portstat \
        iface_namingmode \
        ipfwd \
        monit \
        lldp \
        snmp \
        platform_tests/cli \
        platform_tests/sfp \
        platform_tests/api \
        platform_tests/test_xcvr_info_in_db.py \
        platform_tests/test_power_budget_info.py \
        platform_tests/test_platform_info.py \
        voq/test_voq_init.py \
        voq/test_voq_ipfwd.py \
        voq/test_voq_nbr.py"
        ./run_tests.sh $RUNTEST_CLI_COMMON_OPTS -c "$tests"
  
## Work pending by the Subgroup

The following pending work, if completed by the chassis subgroup, will enable completely automated and T1-compatible run of test cases in the T2 topology.

1. Minigraph generation with VoQ-specific metadata
2. announce_route/fib definition for T2 chassis - proposal sent to MSFT.
5. BGP and Routing - VoQ coverage in the form of new test cases or converting existing ones
6. Everflow - VoQ coverage in the form of new test cases or converting existing ones
7. Fabric - VoQ coverage in the form of new test cases or converting existing ones
8. minigraph generation and T2 tests on multi-asic linecards.

Apart from the BCM issues, the following are pending issues to make tests pass better:
1. config-reload after dynamic modifications in tests and its impact on chassis_db ????
2. Exclusion of Recycle/Inband port from 'show' commands without '-d all' on a single-asic. Part of everflow PR.


