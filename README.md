System: Setup + Acquire Data + Processdata & Predictive + Dashboard

Before you start

- App required:
Thonny for ESP32
MQTT in server and Raspberry Pi 4B
Mongodb

ensure raspberry pi run ubuntu os

**Web Folder**
- Python Library required:
    python: 3.10 or 3.11
    Flask: 3.0.2
    flask-cors: 4.0.0
    pymongo: 4.6.2
    paho-mqtt: 2.0.0 (Required: your code uses the new Version 2 API)
    bcrypt: 4.1.2
    schedule: 1.2.1
    firebase-admin: 6.5.0 (didn't use in final product but still required to install this module to ensure the system doesn't crash out)

**fabric-2.5.4**
- Python Library required:
    Hyperledger Fabric: 2.5.4 (LTS)
    Go (for chaincode): 1.20 or 1.21
    paho-mqtt: 2.0.0
    pymongo: 4.6.2
    requests: 2.31.0

**ESP32 Thonny**
    MicroPython Firmware: v1.22.0 or newer
    micropython-umqtt.simple: 1.3.4 (Install via Thonny Package Manager)

**Raspberry**
    python: 3.10 or 3.11
    scikit-learn: 1.3.2
    pandas: 2.2.1
    numpy: 1.26.4
    joblib: 1.3.2
    psutil: 5.9.8
    paho-mqtt: 2.0.0
    pymongo: 4.6.2


How to run:
Esp 32
Run Thonny Application
Select file: auto-calibrate.py


Raspberry Pi
First Panel
Cd Raspberry
source venv/bin/activate
python rapy.py

Server
First Panel
cd Web
source venv/bin/activate
cd 'host web'
python app.py

Second Panel
cd Web
source venv/bin/activate
cd 'host web'
cd templates
python laptop_water_monitor.py

Third Panel
cd Web
source venv/bin/activate
cd 'host web'
python daily_analysis.py

Forth Panel
cd Web
source venv/bin/activate
cd 'host web'
python dummy.py

Blockchain
First Panel
cd fabric-2.5.4
source venv/bin/activate
export PATH=$PWD/build/bin:$PATH
export FABRIC_CFG_PATH=$PWD/sampleconfig
export CORE_PEER_LOCALMSPID="SampleOrg"
export CORE_PEER_MSPCONFIGPATH=$PWD/sampleconfig/msp
export CORE_PEER_TLS_ENABLED=false
peer lifecycle chaincode package water.tar.gz --path ./smart-contract/water/ --lang golang --label water_1
peer lifecycle chaincode install water.tar.gz
peer lifecycle chaincode approveformyorg -o 127.0.0.1:7050 --channelID mychannel --name water --version 1.0 --package-id YOUR_ID --sequence 2
peer lifecycle chaincode commit -o 127.0.0.1:7050 --channelID mychannel --name water --version 1.0 --sequence 2
python3 mains.py

Second Panel
export PATH=$PWD/build/bin:$PATH
export FABRIC_CFG_PATH=$PWD/sampleconfig
orderer

Third Panel
export PATH=$PWD/build/bin:$PATH
export FABRIC_CFG_PATH=$PWD/sampleconfig
# Use port 9445 for operations to avoid the clash with the Orderer
CORE_OPERATIONS_LISTENADDRESS=127.0.0.1:9445 peer node start --peer-chaincodedev=true

Fourth Panel
hostname -I
cd smart-contract/water
go build
CORE_CHAINCODE_ID_NAME=water:1.0 CORE_PEER_TLS_ENABLED=false ./water -peer.address 127.0.0.1:7052  ←-use your hostname -I address


Note:
1. In the code change WIFI_SSID and WIFI_PASS to your WiFi name and Password.
2. Change etc/mongod.conf ip in server to hostname -i address and restart it.
3. change rapy.py MONGO_HOST variable ip to the ip address that match etc/mongo.conf address.
4. Change flask ip address to hostname -i address

Sensor connection to ESP32-S3:
    pH Sensor: Pin 4
    TDS (Conductivity): Pin 7
    Turbidity: Pin 11
    Temperature (DS18B20): Pin 2
