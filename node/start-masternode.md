# Start Masternode

## Table of Contents

1. [Create a Linux Masternode](start-masternode.md#linux)
2. [Create a Windows Masternode](start-masternode.md#windows)
3. [Create a macOS Masternode](start-masternode.md#macos)

### Create Linux Service to Start Qwertycoind

On this page you will find description how to run Qwertycoind with JSON PRC as linux service. I use Ubuntu server 16.03 x64, but this description you can be applied to any of the linux versions with small changes.

1. Compile the latest Version from Sourcecode

Use this compilation guides: [https://github.com/qwertycoin-org/qwertycoin\#how-to-compile](https://github.com/qwertycoin-org/qwertycoin#how-to-compile)  
For example under Ubuntu:

```text
git clone --recurse-submodules https://github.com/qwertycoin-org/qwertycoin
cd ./qwertycoin
mkdir ./build
cd ./build
cmake -DBUILD_ALL:BOOL=TRUE ..
cmake --build . --config Release
```

* Copy all the compiled files in ./build/src/ to directory _/opt/qwertycoin/_:

```text
sudo mkdir -p /opt/qwertycoin/.Qwertycoin
sudo cp ./src/* /opt/qwertycoin/
```

* To start service we will use user _qwertycoin_, so let's create it and manage permissions:

```text
sudo useradd qwertycoin
sudo groupadd qwertycoin
sudo usermod -a -G qwertycoin qwertycoin
sudo chgrp -R qwertycoin /opt/qwertycoin/
sudo chmod -R 770 /opt/qwertycoin/
```

* Create log file and add permissions to write it:

```text
sudo mkdir -p /var/log/qwertycoin
sudo touch /var/log/qwertycoin/qwertycoind
sudo chgrp -R qwertycoin /var/log/qwertycoin/qwertycoind
sudo chmod -R 770 /var/log/qwertycoin/qwertycoind
```

* Let's check if everything is ok. Try to run daemon with _qwertycoin_ user permission:

```text
sudo -u qwertycoin /opt/qwertycoin/qwertycoind --data-dir=/opt/qwertycoin/.Qwertycoin --log-file=/var/log/qwertycoin/qwertycoind --restricted-rpc --enable-cors=*  --enable-blockchain-indexes --rpc-bind-ip=0.0.0.0 --rpc-bind-port=8197 --fee-address=QWC1L4aAh5i7cbB813RQpsKP6pHXT2ymrbQCwQnQ3DC4QiyuhBUZw8dhAaFp8wH1Do6J9Lmim6ePv1SYFYs97yNV2xvSbTGc7s
```

Stop it via entering `exit` inside daemon session.

* You could pre-download blockchain bootstrap to speed-up process:

```text
cd /opt/qwertycoin/.Qwertycoin
wget https://blockchain.qwertycoin.org/snapshot_$(date "+%Y-%m-%d").tar.gz
tar -xvzf snapshot_$(date "+%Y-%m-%d").tar.gz
rm -f snapshot_$(date "+%Y-%m-%d").tar.gz
sudo chgrp -R qwertycoin /opt/qwertycoin/
sudo chmod -R 770 /opt/qwertycoin/
```

* To start _Qwertycoind_ , we need to create service file in _/etc/systemd/system_:

```text
nano /etc/systemd/system/qwertycoind.service
```

```text
[Unit]
Description=Qwertycoind
Documentation=https://qwertycoin.org
After=syslog.target

[Service]
User=qwertycoin
ExecStart=/opt/qwertycoin/qwertycoind --data-dir=/opt/qwertycoin/.Qwertycoin  \
    --log-file=/var/log/qwertycoin/qwertycoind \
    --restricted-rpc \
    --enable-cors=* \
    --enable-blockchain-indexes \
    --rpc-bind-ip=0.0.0.0 \
    --rpc-bind-port=8197 \
    --fee-address=QWC1L4aAh5i7cbB813RQpsKP6pHXT2ymrbQCwQnQ3DC4QiyuhBUZw8dhAaFp8wH1Do6J9Lmim6ePv1SYFYs97yNV2xvSbTGc7s \
    --contact contact@yourdomain.org
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

Do not forget to change address to your wallet!

* Run service:

```text
sudo systemctl daemon-reload
sudo systemctl enable qwertycoind.service
sudo systemctl start qwertycoind.service
```

* To check service status:

```text
systemctl status qwertycoind.service
```

```text
● qwertycoind.service - Qwertycoind
   Loaded: loaded (/etc/systemd/system/qwertycoind.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2018-06-08 04:11:30 EDT; 37s ago
     Docs: https://qwertycoin.org
 Main PID: 1882 (Qwertycoind)
   CGroup: /system.slice/qwertycoind.service
           └─1882 /opt/qwertycoin/qwertycoind --data-dir=/opt/qwertycoin/.Qwertycoin
lines 1-7/7 (END)
```

### Create a Masternode on Windows using qwertycoind.exe

On this page you will find description how to run Qwertycoind I use Windowx x64 Enterprise, but this description you can be applied to any Windows Version \(&gt; Windows 7\).

* Compile the latest Version from Sourcecode **OR! download a prebuilt Release from here**: [https://releases.qwertycoin.org/](https://releases.qwertycoin.org/)

Use this compilation guides: [https://github.com/qwertycoin-org/qwertycoin\#how-to-compile](https://github.com/qwertycoin-org/qwertycoin#how-to-compile)

For example under Windows:

```text
git clone https://github.com/qwertycoin-org/qwertycoin
cd qwertycoin
md build
cd build
cmake -G "Visual Studio 15 2017 Win64" -DBUILD_ALL:BOOL=TRUE ..
cmake --build . --config Release
```

If you use a prebuilt Version, download it, install it and forward to Step 3

* Self compiled Versions: If all went well, it will complete successfully, and you will find all your binaries in the `.\build\src\Release` directory

Open a CMD on Windows and navigate to this folder. For example: \(the folder structure could be different.\)

`cd C:\Github\qwertycoin\build\src\Release`

* Prebuilt Versions:

After installation you will find the binaries in:

`C:\Program Files\qwertycoin\bin`

* Start the Masternode in a CMD

Copy and paste the following \(that's one single line!\):

`qwertycoind.exe --restricted-rpc --enable-cors=* --enable-blockchain-indexes --rpc-bind-ip=0.0.0.0 --rpc-bind-port=8197 --fee-address=QWC1L4aAh5i7cbB813RQpsKP6pHXT2ymrbQCwQnQ3DC4QiyuhBUZw8dhAaFp8wH1Do6J9Lmim6ePv1SYFYs97yNV2xvSbTGc7s`

If you want you can replace the developers fee address with your own QWC Wallet address \(Not necessary\)

* Done

