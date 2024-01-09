# srsRAN_4G

090124
- install open5gs https://open5gs.org/open5gs/docs/tutorial/01-your-first-lte/
- git push all files

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

### 1. install blade RF 
follow link: https://github.com/Nuand/bladeRF
```
git clone https://github.com/Nuand/bladeRF.git
mkdir -p build
cd build
cmake [options] ../
make
sudo make install
sudo ldconfig
```
### 2. install srsRAN_4G
if you already install srsRAN_4G but haven't installed bladeRF, you will have to install from source again.

install pre-processor
```
sudo apt-get install build-essential cmake libfftw3-dev libmbedtls-dev libboost-program-options-dev libconfig++-dev libsctp-dev
```
then install srsRAN_4G (make test not really required)
```
git clone https://github.com/srsRAN/srsRAN_4G.git
cd srsRAN_4G
mkdir build
cd build
cmake ../
make
make test

sudo make install
srsran_install_configs.sh user
```
## 3. edit files
## files to edit in srsran: epc.conf, user_db.conf, rr.conf, enb.conf
### 3.1. epc.conf
change
- mcc = 901
- mnc = 70 (follow the number of the SIM)
- apn = srsapn

### 3.2. user_db.csv
ue3,mil,901700000037982,6c03137e507414d32a49ade8ff4aa820,opc,1562d329051a61f4a898fde234507670,9000,000000000000,9,dynamic

| IMSI  | ICCID | ACC  | PIN1 | PUK1  | PIN2 | PUK2  | Ki | OPC |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 901700000037982  | 8988211000000379829  | 0004  | 8276  | 91676454  | 1098  | 34819185  | 6C03137E507414D32A49ADE8FF4AA820 | 1562D329051A61F4A898FDE234507670 |

### 3.3. rr.conf
[cell_list]
dl_earfcn = 1575

### 3.4. enb.conf
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
go to local `localhost:3000` to add subscriber with info in user_db.csv /
username: admin /
password: 1423 /
at 09/01/23, the webui change to `localhost:9999` /
also don't forget to start open5gs service



## run code
### 0. start masq
```
>> cd ~/.config/srsran
>> ifconfig
>> route
>> sudo srsepc_if_masq.sh wlp9s0
srs_spgw_sgi
```
without the masq ue cannot access internet, also the epc screen won't show `Sending EMM Information`
check the mask interface using command `route`


### 1. run epc and enb
```
>> cd ~/.config/srsran
>> sudo srsepc epc.conf
```
in case port binding
```
netstat -tulpn
sudo kill <pid>
```
and in another window
```
>> cd ~/.config/srsran
>> sudo srsenb enb.conf
```

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

## pice of successful result
### 1. EPC
```
---  Software Radio Systems EPC  ---

Reading configuration file epc.conf...
HSS Initialized.
MME S11 Initialized
MME GTP-C Initialized
MME Initialized. MCC: 0xf901, MNC: 0xff70
SPGW GTP-U Initialized.
SPGW S11 Initialized.
SP-GW Initialized.
Received S1 Setup Request.
S1 Setup Request - eNB Name: srsenb01, eNB id: 0x19b
S1 Setup Request - MCC:901, MNC:70
S1 Setup Request - TAC 7, B-PLMN 0x9f107
S1 Setup Request - Paging DRX v128
Sending S1 Setup Response
Initial UE message: LIBLTE_MME_MSG_TYPE_TRACKING_AREA_UPDATE_REQUEST
Received Initial UE message -- Tracking Area Update Request
Tracking Area Update Request -- S-TMSI 0xb2c5eab5
Tracking Area Update Request -- eNB UE S1AP Id 1
Warning: Tracking area update requests are not handled yet.
Initial UE message: LIBLTE_MME_MSG_TYPE_ATTACH_REQUEST
Received Initial UE message -- Attach Request
Attach request -- M-TMSI: 0xb2c5eab5
Attach request -- eNB-UE S1AP Id: 2
Attach request -- Attach type: 2
Attach Request -- UE Network Capabilities EEA: 11110000
Attach Request -- UE Network Capabilities EIA: 11110000
Attach Request -- MS Network Capabilities Present: true
PDN Connectivity Request -- EPS Bearer Identity requested: 0
PDN Connectivity Request -- Procedure Transaction Id: 1
PDN Connectivity Request -- ESM Information Transfer requested: true
UL NAS: Received Identity Response
ID Response -- IMSI: 901700000037982
Downlink NAS: Sent Authentication Request
UL NAS: Authentication Failure
Authentication Failure -- Synchronization Failure
Downlink NAS: Sent Authentication Request
UL NAS: Received Authentication Response
Authentication Response -- IMSI 901700000037982
UE Authentication Accepted.
Generating KeNB with UL NAS COUNT: 0
Downlink NAS: Sending NAS Security Mode Command.
UL NAS: Received Security Mode Complete
Security Mode Command Complete -- IMSI: 901700000037982
Sending ESM information request
UL NAS: Received ESM Information Response
ESM Info: APN internet
ESM Info: 4 Protocol Configuration Options
Getting subscription information -- QCI 9
Sending Create Session Request.
Creating Session Response -- IMSI: 901700000037982
Creating Session Response -- MME control TEID: 1
Received GTP-C PDU. Message type: GTPC_MSG_TYPE_CREATE_SESSION_REQUEST
SPGW: Allocated Ctrl TEID 1
SPGW: Allocated User TEID 1
SPGW: Allocate UE IP 172.16.0.2
Received Create Session Response
Create Session Response -- SPGW control TEID 1
Create Session Response -- SPGW S1-U Address: 127.0.1.100
SPGW Allocated IP 172.16.0.2 to IMSI 901700000037982
Adding attach accept to Initial Context Setup Request
Sent Initial Context Setup Request. E-RAB id 5 
Received Initial Context Setup Response
E-RAB Context Setup. E-RAB id 5
E-RAB Context -- eNB TEID 0x1; eNB GTP-U Address 127.0.1.1
UL NAS: Received Attach Complete
Unpacked Attached Complete Message. IMSI 901700000037982
Unpacked Activate Default EPS Bearer message. EPS Bearer id 5
Received GTP-C PDU. Message type: GTPC_MSG_TYPE_MODIFY_BEARER_REQUEST
Sending EMM Information
```

### 2. ENB
```
---  Software Radio Systems LTE eNodeB  ---

Reading configuration file enb.conf...
WARNING: cpu0 scaling governor is not set to performance mode. Realtime processing could be compromised. Consider setting it to performance mode before running the application.

Built in Release mode using commit af4b9589b on branch master.

Opening 2 channels in RF device=bladeRF with args=default
Supported RF device list: UHD bladeRF zmq file
Opening bladeRF...
Set RX sampling rate 1.92 Mhz, filter BW: 1.92 Mhz

==== eNodeB started ===
Type <t> to view trace
Set RX sampling rate 3.84 Mhz, filter BW: 3.07 Mhz
Setting manual TX/RX offset to 27 samples
Setting frequency: DL=1842.5 Mhz, UL=1747.5 MHz for cc_idx=0 nof_prb=15
set TX frequency to 1842500000
set TX frequency to 1842500000
set RX frequency to 1747500000
set RX frequency to 1747500000
RACH:  tti=181, cc=0, pci=1, preamble=32, offset=23, temp_crnti=0x46
RACH:  tti=181, cc=0, pci=1, preamble=35, offset=29, temp_crnti=0x47
Disconnecting rnti=0x46.
RACH:  tti=71, cc=0, pci=1, preamble=10, offset=29, temp_crnti=0x48
User 0x48 connected
Disconnecting rnti=0x47.
Disconnecting rnti=0x48.
RACH:  tti=7571, cc=0, pci=1, preamble=43, offset=29, temp_crnti=0x49
User 0x49 connected
```
### 3. ENB trace mode
```
               -----------------DL----------------|-------------------------UL-------------------------
rat  pci rnti  cqi  ri  mcs  brate   ok  nok  (%) | pusch  pucch  phr  mcs  brate   ok  nok  (%)    bsr
lte    1   47  n/a   0    0      0    0    0   0% |   n/a    n/a    0    0      0    0    0   0%    0.0
lte    1   48   15   1   20   308k   57    0   0% |  23.2   18.0   40   22   635k  118    0   0%    0.0
lte    1   47  n/a   0    0      0    0    0   0% |   n/a    n/a    0    0      0    0    0   0%    0.0
lte    1   48   15   1   20    99k   15    0   0% |  17.6   15.3   40   17   114k   32    0   0%    0.0
lte    1   47    4   0    0      0    0    3 100% |   n/a    2.0    0    0      0    0    0   0%    0.0
lte    1   48   15   1    5    599    3    0   0% |  17.2   15.7   40   17   8.3k    2    0   0%    0.0
lte    1   47  n/a   0    0      0    0    0   0% |   n/a    n/a    0    0      0    0    0   0%    0.0
lte    1   48   15   1    1    112    2    0   0% |  22.5   20.3   40   20   8.5k    2    0   0%    0.0
lte    1   47  n/a   0    0      0    0    0   0% |   n/a    n/a    0    0      0    0    0   0%    0.0
lte    1   48   15   1    0      0    0    0   0% |   n/a   99.9    0    0      0    0    0   0%    0.0
lte    1   47   11   0    0      0    0    3 100% |   n/a   -0.1    0    0      0    0    0   0%    0.0
lte    1   48   15   1    0      0    0    0   0% |   n/a   99.9    0    0      0    0    0   0%    0.0
lte    1   47  n/a   0    0      0    0    0   0% |   n/a    n/a    0    0      0    0    0   0%    0.0
lte    1   48   15   1    0      0    0    0   0% |   n/a   99.9    0    0      0    0    0   0%    0.0
lte    1   47    9   0    0      0    0    3 100% |   n/a    1.6    0    0      0    0    0   0%    0.0
lte    1   48   15   1    9    13k   15    0   0% |  20.8   19.5   40   20    51k   15    0   0%    0.0
lte    1   47   14   0    0      0    0    3 100% |   n/a   -1.8    0    0      0    0    0   0%    0.0
lte    1   48   15   1    8   7.2k   11    0   0% |  20.3   18.0   40   19    36k   10    0   0%    0.0
lte    1   47  n/a   0    0      0    0    0   0% |   n/a    n/a    0    0      0    0    0   0%    0.0
lte    1   48   15   1   19    59k   10    0   0% |  21.3   18.0   40   20    13k    3    0   0%    0.0
lte    1   47  n/a   0    0      0    0    0   0% |   n/a    n/a    0    0      0    0    0   0%    0.0
lte    1   48   15   1    0      0    0    0   0% |   n/a   99.9    0    0      0    0    0   0%    0.0
```

### 4. UE speedtest
https://github.com/pchat-imm/srsRAN/assets/40858099/fa045b5b-91ad-4867-8c23-63f9bcb1f2c2

### 5. update epc result
```
chatchamon@chatchamon-ThinkPad-L14-Gen-2:~/.config/srsran$ sudo srsepc epc.conf 

Built in Release mode using commit eea87b1d8 on branch master.


---  Software Radio Systems EPC  ---

Reading configuration file epc.conf...
HSS Initialized.
MME S11 Initialized
MME GTP-C Initialized
MME Initialized. MCC: 0xf901, MNC: 0xff70
SPGW GTP-U Initialized.
SPGW S11 Initialized.
SP-GW Initialized.
Received S1 Setup Request.
S1 Setup Request - eNB Name: srsenb01, eNB id: 0x19b
S1 Setup Request - MCC:901, MNC:70
S1 Setup Request - TAC 7, B-PLMN 0x9f107
S1 Setup Request - Paging DRX v128
Sending S1 Setup Response
Initial UE message: LIBLTE_MME_MSG_TYPE_TRACKING_AREA_UPDATE_REQUEST
Received Initial UE message -- Tracking Area Update Request
Tracking Area Update Request -- S-TMSI 0x121c10e9
Tracking Area Update Request -- eNB UE S1AP Id 1
Warning: Tracking area update requests are not handled yet.
Initial UE message: LIBLTE_MME_MSG_TYPE_ATTACH_REQUEST
Received Initial UE message -- Attach Request
Attach request -- M-TMSI: 0x121c10e9
Attach request -- eNB-UE S1AP Id: 2
Attach request -- Attach type: 2
Attach Request -- UE Network Capabilities EEA: 11110000
Attach Request -- UE Network Capabilities EIA: 11110000
Attach Request -- MS Network Capabilities Present: true
PDN Connectivity Request -- EPS Bearer Identity requested: 0
PDN Connectivity Request -- Procedure Transaction Id: 1
PDN Connectivity Request -- ESM Information Transfer requested: true
UL NAS: Received Identity Response
ID Response -- IMSI: 901700000037982
Downlink NAS: Sent Authentication Request
UL NAS: Authentication Failure
Authentication Failure -- Synchronization Failure
Downlink NAS: Sent Authentication Request
UL NAS: Received Authentication Response
Authentication Response -- IMSI 901700000037982
UE Authentication Accepted.
Generating KeNB with UL NAS COUNT: 0
Downlink NAS: Sending NAS Security Mode Command.
UL NAS: Received Security Mode Complete
Security Mode Command Complete -- IMSI: 901700000037982
Sending ESM information request
UL NAS: Received ESM Information Response
ESM Info: APN srsapn
ESM Info: 4 Protocol Configuration Options
Getting subscription information -- QCI 9
Sending Create Session Request.
Creating Session Response -- IMSI: 901700000037982
Creating Session Response -- MME control TEID: 1
Received GTP-C PDU. Message type: GTPC_MSG_TYPE_CREATE_SESSION_REQUEST
SPGW: Allocated Ctrl TEID 1
SPGW: Allocated User TEID 1
SPGW: Allocate UE IP 172.16.0.2
Received Create Session Response
Create Session Response -- SPGW control TEID 1
Create Session Response -- SPGW S1-U Address: 127.0.1.100
SPGW Allocated IP 172.16.0.2 to IMSI 901700000037982
Adding attach accept to Initial Context Setup Request
Sent Initial Context Setup Request. E-RAB id 5 
Received Initial Context Setup Response
E-RAB Context Setup. E-RAB id 5
E-RAB Context -- eNB TEID 0x1; eNB GTP-U Address 127.0.1.1
UL NAS: Received Attach Complete
Unpacked Attached Complete Message. IMSI 901700000037982
Unpacked Activate Default EPS Bearer message. EPS Bearer id 5
Received GTP-C PDU. Message type: GTPC_MSG_TYPE_MODIFY_BEARER_REQUEST
Sending EMM Information
Received UE Context Release Request. MME-UE S1AP Id 1
No UE context to release found. MME-UE S1AP Id: 1
Received UE Context Release Request. MME-UE S1AP Id 2
There are active E-RABs, send release access bearers request
Received GTP-C PDU. Message type: GTPC_MSG_TYPE_RELEASE_ACCESS_BEARERS_REQUEST
Received UE Context Release Complete. MME-UE S1AP Id 2
UE Context Release Completed.
Initial UE message: NAS Message Type Unknown
Received Initial UE message -- Service Request
Service request -- S-TMSI 0xf973b6cb
Service request -- eNB UE S1AP Id 3
Service Request -- Short MAC valid
Service Request -- User is ECM DISCONNECTED
UE previously assigned IP: 172.16.0.2
Generating KeNB with UL NAS COUNT: 3
UE Ctr TEID 0
Sent Initial Context Setup Request. E-RAB id 5 
Received Initial Context Setup Response
E-RAB Context Setup. E-RAB id 5
E-RAB Context -- eNB TEID 0x2; eNB GTP-U Address 127.0.1.1
Initial Context Setup Response triggered from Service Request.
Sending Modify Bearer Request.
Received GTP-C PDU. Message type: GTPC_MSG_TYPE_MODIFY_BEARER_REQUEST
Received UE Context Release Request. MME-UE S1AP Id 3
There are active E-RABs, send release access bearers request
Received GTP-C PDU. Message type: GTPC_MSG_TYPE_RELEASE_ACCESS_BEARERS_REQUEST
Received UE Context Release Complete. MME-UE S1AP Id 3
UE Context Release Completed.
Initial UE message: NAS Message Type Unknown
Received Initial UE message -- Service Request
Service request -- S-TMSI 0xf973b6cb
Service request -- eNB UE S1AP Id 4
Service Request -- Short MAC valid
Service Request -- User is ECM DISCONNECTED
UE previously assigned IP: 172.16.0.2
Generating KeNB with UL NAS COUNT: 4
UE Ctr TEID 0
Sent Initial Context Setup Request. E-RAB id 5 
Received Initial Context Setup Response
E-RAB Context Setup. E-RAB id 5
E-RAB Context -- eNB TEID 0x3; eNB GTP-U Address 127.0.1.1
Initial Context Setup Response triggered from Service Request.
Sending Modify Bearer Request.
Received GTP-C PDU. Message type: GTPC_MSG_TYPE_MODIFY_BEARER_REQUEST
```

