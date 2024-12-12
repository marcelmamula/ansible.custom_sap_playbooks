# SAP S4HANA Distributed HA system

## Scenario specifics:
1. 6 server system
  - 2 server cluster for SAP HANA
  - 2 server cluster for SAP ASCS/ERS
  - 1 server for PAS (Primary Application Server)
  - 1 server for AAS (Additional Application Server)
2. SAP HANA installed, replication enabled and cluster configured
3. SAP ASCS/ERS installed, cluster configured
4. SAP PAS installed and executed initial S4HANA database load
5. SAP AAS installed

## Available cloud configurations
Scenario was tested with following cloud platforms:
- AWS
- Google Cloud
- MS Azure

## Execution runtime
End to end execution can be completed in approximately 2 to 3 hours using predefined host configuration.</br>
Majority of time is taken by SAP S4HANA database load which takes around 1,5 hours.

## Software used during test
Contents of NFS share that was used during testing:

Database software
```console
IMDB_AFL20_075_0-80001894.SAR
IMDB_CLIENT20_019_21-80002082.SAR
IMDB_LCAPPS_2075_0-20010426.SAR
IMDB_SERVER20_075_0-80002031.SAR
SAPCAR_1200-70007716.EXE
SAPHOSTAGENT62_62-80004822.SAR
```

Application software
```console
IMDB_CLIENT20_019_21-80002082.SAR
S4CORE108_INST_EXPORT_1.zip
S4CORE108_INST_EXPORT_10.zip
S4CORE108_INST_EXPORT_11.zip
S4CORE108_INST_EXPORT_12.zip
S4CORE108_INST_EXPORT_13.zip
S4CORE108_INST_EXPORT_14.zip
S4CORE108_INST_EXPORT_15.zip
S4CORE108_INST_EXPORT_16.zip
S4CORE108_INST_EXPORT_17.zip
S4CORE108_INST_EXPORT_18.zip
S4CORE108_INST_EXPORT_19.zip
S4CORE108_INST_EXPORT_2.zip
S4CORE108_INST_EXPORT_20.zip
S4CORE108_INST_EXPORT_21.zip
S4CORE108_INST_EXPORT_22.zip
S4CORE108_INST_EXPORT_23.zip
S4CORE108_INST_EXPORT_24.zip
S4CORE108_INST_EXPORT_25.zip
S4CORE108_INST_EXPORT_26.zip
S4CORE108_INST_EXPORT_27.zip
S4CORE108_INST_EXPORT_28.zip
S4CORE108_INST_EXPORT_29.zip
S4CORE108_INST_EXPORT_3.zip
S4CORE108_INST_EXPORT_30.zip
S4CORE108_INST_EXPORT_4.zip
S4CORE108_INST_EXPORT_5.zip
S4CORE108_INST_EXPORT_6.zip
S4CORE108_INST_EXPORT_7.zip
S4CORE108_INST_EXPORT_8.zip
S4CORE108_INST_EXPORT_9.zip
S4HANAOP108_ERP_LANG_EN.SAR
SAPCAR_1200-70007716.EXE
SAPEXEDB_51-70007806.SAR
SAPEXE_51-70007807.SAR
SAPHOSTAGENT62_62-80004822.SAR
SWPM20SP16_2-80003424.SAR
igsexe_4-70005417.sar
igshelper_17-10010245.sar
```
**NOTE: This list was used during testing and it is not mandatory to use same**