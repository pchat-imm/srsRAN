# srsRAN_4G

## setup
<img src = https://github.com/pchat-imm/srsRAN/assets/40858099/71fd2ecc-add7-4c4d-bcdf-b728c3c646d9 width=50% height=50% />

- SDR: BladeRF Micro 2.0
- 4 LTE Antennas
- open5GS for core network
- Samsung galaxy j7 as UE
- Sysmocom sim card

## links to follow
for setup epc, enb: https://docs.srsran.com/projects/4g/en/latest/app_notes/source/cots_ue <br />
for add subsciber to open5gs:  https://open5gs.org/open5gs/docs/tutorial/01-your-first-lte/ <br />
for start services of open5gs: https://open5gs.org/open5gs/docs/troubleshoot/01-simple-issues/ <br />
for doing the project: https://github.com/fllay/LTE/wiki

## files to edit in srsran: epc.conf, user_db.conf, rr.conf, enb.conf
### 1. epc.conf
change
- mcc = 901
- mnc = 70 (follow the number of the SIM)
- apn = srsapn

### 2. user_db.csv
ue3,mil,901700000037982,6c03137e507414d32a49ade8ff4aa820,opc,1562d329051a61f4a898fde234507670,9000,000000000000,9,dynamic

| IMSI  | ICCID | ACC  | PIN1 | PUK1  | PIN2 | PUK2  | Ki | OPC |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 901700000037982  | 8988211000000379829  | 0004  | 8276  | 91676454  | 1098  | 34819185  | 6C03137E507414D32A49ADE8FF4AA820 | 1562D329051A61F4A898FDE234507670 |

### 3. rr.conf
[cell_list]
dl_earfcn = 1575

### enb.conf
[enb]
- mcc = 901
- mnc = 70
- n_prb = 15
- tm = 4
- nof_port = 2
[rf]
- tx_gain = 80
- #rx-gain = auto
- device_name = bladeRF
- time_adv_nsamples = 27

## Config open5gs
go to local `localhost:3000` to add subscriber with info in user_db.csv

## run code
### 1. run epc and enb
```
>> cd ~/.config/srsran
>> sudo srsepc epc.conf
```

and in another window
```
>> cd ~/.config/srsran
>> sudo srsenb enb.conf
```

then add masq
'sudo srsepc_if_masq.sh srs_spgw_sgi'
can check this interface with 'route' command

### 2. set up phone
- add APN
```
setting > mobile network > access point name
- name = srsapn
- apn = srsapn
- mcc = 901
- mnc = 70
- mobile virtual network operator type = None
```
- select network
```
setting > mobile network > network operator > search network
select one that is standout = Software Radio System RAN
```
or
```
setting > mobile network > network operator > select network automatic
```
<img src = https://github.com/pchat-imm/srsRAN/assets/40858099/50f94e29-b831-491b-a956-ac484c2ceee6 width=40% height=40% /> <br />

make sure apn is correct <br />
then check `setting -> about device -> status -> sim status` <br />
and it should show connect ip address, mobile network state <br />
<img src = https://github.com/pchat-imm/srsRAN/assets/40858099/3df1f8f2-a99b-4d8d-8708-eb3cbb64a9d2 width=40% height=40% />
