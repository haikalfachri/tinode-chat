### Accessing server from local network in wsl

- Make sure that devices are already connected to network before doing the commands
- Make sure that server is not up before doing the commands
- {NETWORK_IP_ADDR} is ip of network
- {WSL_IP_ADDR} is ip of wsl obtained via ifconfig

1. Open port 16060 (gRPC server)
```
netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=16060
```

2. Open port 6060 (Tinode server)
```
netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=6060
```

3. Connect port 16060 in windows from wsl
```
netsh interface portproxy add v4tov4 listenport=16060 listenaddress={NETWORK_IP_ADDR} connectport=16060 connectaddress={WSL_IP_ADDR}
```

4. Connect port 6060 in windows from wsl
```
netsh interface portproxy add v4tov4 listenport=6060 listenaddress={NETWORK_IP_ADDR} connectport=6060 connectaddress={WSL_IP_ADDR}
```