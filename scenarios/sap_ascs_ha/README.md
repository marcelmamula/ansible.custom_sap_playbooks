# SAP Netweaver ASCS ERS cluster

## Scenario specifics:
1. Two server cluster with minimal footprint for test purposes
2. SAP Netweaver ASCS installed on first server
3. SAP Netweaver ERS installed on second server
4. Pacemaker cluster configured
    - [Simple Mount cluster](https://documentation.suse.com/sbp/sap-15/html/SAP-S4HA10-setupguide-simplemount-sle15/) is configured by default on supported OS.
    - [Classic cluster](https://documentation.suse.com/sbp/sap-15/html/SAP-S4HA10-setupguide-sle15) cluster is configured variable is set: `sap_ha_pacemaker_cluster_nwas_abap_ascs_ers_simple_mount: false`


## Available cloud configurations
Scenario was tested with following cloud platforms:
- AWS
- Google Cloud
- MS Azure

## Execution runtime
End to end execution can be completed in approximately 30 minutes using predefined host configuration.

## Software used during test
Contents of NFS share that was used during testing:
```console
SWPM10SP40_0-20009701.SAR
SAPEXE_200-80007612.SAR
SAPEXEDB_200-80007611.SAR
igsexe_4-80007786.sar
igshelper_17-10010245.sar
SAPCAR_1200-70007716.EXE
SAPHOSTAGENT61_61-80004822.SAR
```