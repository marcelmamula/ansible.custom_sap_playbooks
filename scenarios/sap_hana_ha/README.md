# SAP HANA HA Scale-up cluster

## Scenario specifics:
1. Two server cluster with minimal footprint for test purposes
2. SAP HANA 2.0 installed on both servers
3. SAP HANA Replication enabled
4. Pacemaker cluster configured
    - [SAPHanaSR-angi cluster](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-angi-perfopt-15/index.html) is configured if SAPHanaSR-angi package is available (SLES4SAP 15 SP5+)
    - [SAPHana classic](https://documentation.suse.com/sbp/sap-15/html/SLES4SAP-hana-sr-guide-PerfOpt-15/index.html) cluster is configured if SAPHanaSR-angi is not available.


## Available cloud configurations
Scenario was tested with following cloud platforms:
- AWS
- Google Cloud (TODO: Resolve HA Cluster to haproxy issues in sap_ha_pacemaker_cluster role)
- MS Azure

## Execution runtime
End to end execution can be completed in approximately 30 minutes using predefined host configuration.

## Software used during test
Contents of NFS share that was used during testing:
```console
IMDB_CLIENT20_019_21-80002082.SAR
IMDB_SERVER20_076_0-80002031.SAR
SAPCAR_1200-70007716.EXE
SAPHOSTAGENT62_62-80004822.SAR
```
**NOTE: This list was used during testing and it is not mandatory to use same**