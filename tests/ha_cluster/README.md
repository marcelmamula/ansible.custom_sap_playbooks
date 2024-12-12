# Playbook for ha_cluster testing
This Ansible playbook is only for direct testing of ha_cluster role and it is not valid SAP deployment scenario.

## Test details:
This test playbook aims to create cluster using `fedora.linux_system_roles.ha_cluster` directly.
Cluster contains all various resources and constraints.

## Example of generated cluster
Cluster status after execution
```console
Full List of Resources:
  * rsc_fence_aws_1     (stonith:external/ec2):  Started hc1node1
  * rsc_fence_aws_2     (stonith:external/ec2):  Started hc1node2
  * rsc_dummy_hc1node1_1        (ocf::pacemaker:Dummy):  Started hc1node1
  * rsc_dummy_hc1node1_2        (ocf::pacemaker:Dummy):  Started hc1node2
  * rsc_dummy_hc1node2  (ocf::pacemaker:Dummy):  Started hc1node2
  * rsc_dummy1  (ocf::pacemaker:Dummy):  Started hc1node1
  * rsc_dummy5  (ocf::pacemaker:Dummy):  Stopped
  * rsc_dummy6  (ocf::pacemaker:Dummy):  Stopped
  * rsc_dummy7  (ocf::pacemaker:Dummy):  Stopped
  * rsc_dummy8  (ocf::pacemaker:Dummy):  Stopped
  * Resource Group: grp_vip1_healthcheck1:
    * rsc_vip_ipaddr_1  (ocf::heartbeat:IPaddr2):        Started hc1node1
    * rsc_health_check_1        (ocf::heartbeat:anything):       Started hc1node1
  * Resource Group: grp_vip2_healthcheck2:
    * rsc_vip_ipaddr_2  (ocf::heartbeat:IPaddr2):        Started hc1node2
    * rsc_health_check_2        (ocf::heartbeat:anything):       Started hc1node2
  * Clone Set: cln_rsc_dummy_cloned [rsc_dummy_cloned]:
    * Started: [ hc1node1 hc1node2 ]
```
**NOTE: Stopped resources and result of ticket constraints.**

Cluster configuration after execution
```console
node 1: hc1node1
node 2: hc1node2
primitive rsc_dummy1 ocf:pacemaker:Dummy
primitive rsc_dummy5 ocf:pacemaker:Dummy
primitive rsc_dummy6 ocf:pacemaker:Dummy
primitive rsc_dummy7 ocf:pacemaker:Dummy
primitive rsc_dummy8 ocf:pacemaker:Dummy
primitive rsc_dummy_cloned ocf:pacemaker:Dummy \
        op monitor interval=30s
primitive rsc_dummy_hc1node1_1 ocf:pacemaker:Dummy
primitive rsc_dummy_hc1node1_2 ocf:pacemaker:Dummy
primitive rsc_dummy_hc1node2 ocf:pacemaker:Dummy
primitive rsc_fence_aws_1 stonith:external/ec2 \
        params pcmk_delay_max=30 tag=pacemaker
primitive rsc_fence_aws_2 stonith:external/ec2 \
        params pcmk_delay_max=30 tag=pacemaker
primitive rsc_health_check_1 anything \
        params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62620,backlog=10,fork,reuseaddr /dev/null" \
        meta target-role=Started \
        op monitor interval=30s timeout=60s
primitive rsc_health_check_2 anything \
        params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:62621,backlog=10,fork,reuseaddr /dev/null" \
        meta target-role=Started \
        op monitor interval=30s timeout=60s
primitive rsc_vip_ipaddr_1 IPaddr2 \
        params ip=192.168.10.10 nic=eth0 cidr_netmask=32
primitive rsc_vip_ipaddr_2 IPaddr2 \
        params ip=192.168.10.11 nic=eth0 cidr_netmask=32
group grp_vip1_healthcheck1 rsc_vip_ipaddr_1 rsc_health_check_1 \
        meta resource-stickiness=3000
group grp_vip2_healthcheck2 rsc_vip_ipaddr_2 rsc_health_check_2 \
        meta resource-stickiness=4000
clone cln_rsc_dummy_cloned rsc_dummy_cloned \
        meta clone-max=2 clone-node-max=1 interleave=true
colocation col_vip1_separate_vip2 -5000: grp_vip2_healthcheck2 grp_vip1_healthcheck1:Started
location loc_dummy_on_hc1node1 /dummy_hc1node1_1/ role=Started -inf: hc1node2
location loc_rsc_dummy_hc1node2 rsc_dummy_hc1node2 5000: hc1node2
order ord_grp2_dummy1 Optional: grp_vip2_healthcheck2:start rsc_dummy1:start symmetrical=false
colocation set_colocation_1 inf: ( rsc_dummy5 rsc_dummy6 ) [ rsc_dummy7 rsc_dummy8 sequential=true ]
order set_order_1 Mandatory: [ rsc_dummy8 rsc_dummy7 ]
fencing_topology \
        hc1node1: rsc_fence_aws_1 rsc_fence_aws_2
rsc_ticket set_ticket_1 ticket2: ( rsc_dummy7 rsc_dummy8 )
rsc_ticket tck_rsc_dummy5 ticket1: rsc_dummy5 loss-policy=stop
property cib-bootstrap-options: \
        have-watchdog=false \
        dc-version="2.1.7+20231219.0f7f88312-150600.6.3.1-2.1.7+20231219.0f7f88312" \
        cluster-infrastructure=corosync \
        maintenance-mode=false \
        stonith-enabled=True \
        stonith-timeout=900 \
        concurrent-fencing=True
rsc_defaults build-resource-defaults: \
        resource-stickiness=1 \
        migration-threshold=3
op_defaults op-options: \
        timeout=600 \
        record-pending=True
```

## Available cloud configurations
Playbook was tested with following cloud platforms:
- AWS
