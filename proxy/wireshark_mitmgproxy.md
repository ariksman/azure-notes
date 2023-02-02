# Inspecting traffic with Wireshark + Mitmproxy

After installation of both Mitmproxy and wireshark, its easy to setup the startup with environmental variables via script. Save the following code snippet for example as `start_mitmproxy.cmd` on desktop folder.

```shell
@echo off
set MITMPROXY_SSLKEYLOGFILE=C:\Users\{UserName}\.mitmproxy\sslkeylogfile.txt
start mitmproxy
```
The documentation for the certificate process is available at: https://docs.mitmproxy.org/stable/overview-getting-started/

After certificate setup is ready configure Windows to use proxy at `127.0.0.1:8080`

<img src="win_proxy.png" width="300" />

After this, setup Wireshark TLS settings to use the same `sslkeylogfile.txt` for decrypting the traffic.

<img src="wireshark_settings.png" width="400" />

View of the proxy window

<img src="mitmproxy.png" width="1024" />

To start listening the `127.0.0.1` with wireshark, select right interface at the startup

<img src="wireshark_capture_settings.png" width="400" />

And if everything is configured correctly, all traffic should be visible in the wireshark

<img src="wireshark_view.png" width="1024" />

## Wireshark filtering

Usefull filtering methods for wireshark:

```
http.host == "*.azure-api.net"
http.request.method == "GET" or http.request.method == "POST"
```
