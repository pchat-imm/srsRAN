# srsRAN_4G

## setup
![IMG_0294](https://github.com/pchat-imm/srsRAN/assets/40858099/456cec84-b52e-474e-bf88-847b4c76c26c)
- SDR: BladeRF Micro 2.0
- 4 LTE Antennas
- open5GS for core network
- Samsung galaxy j7 as UE
- Sysmocom sim card

## links to follow
for setup epc, enb: https://docs.srsran.com/projects/4g/en/latest/app_notes/source/cots_ue
for add subsciber to open5gs:  https://open5gs.org/open5gs/docs/tutorial/01-your-first-lte/
for start services of open5gs: https://open5gs.org/open5gs/docs/troubleshoot/01-simple-issues/
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
go to local 'localhost:3000'

