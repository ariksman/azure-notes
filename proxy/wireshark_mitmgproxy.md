# Inpecting traffic with Wireshark + Mitmproxy

After installation of both Mitmproxy and wireshark, its easy to setup the startup with environmental variables via script
```shell
@echo off
set MITMPROXY_SSLKEYLOGFILE=C:\Users\{UerName}\.mitmproxy\sslkeylogfile.txt
start mitmproxy
```

Then setup windows to use proxy at `127.0.0.1:8080`

After this setup Wireshark TLS settings to use the same `sslkeylogfile.txt` for decrypting the traffic.

