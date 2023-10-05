## Accessing server from local network in wsl

- Make sure that devices are already connected to network before doing the commands
- Make sure that server is not up before doing the commands
- {NETWORK_IP_ADDR} is ip of your network
- {WSL_IP_ADDR} is ip of wsl obtained via ifconfig

1. Open cmd as admin in windows

2. Open port 16060 (gRPC server)
```
netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=16060
```

3. Open port 6060 (Tinode server)
```
netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=6060
```

4. Connect port 16060 in windows from wsl
```
netsh interface portproxy add v4tov4 listenport=16060 listenaddress={NETWORK_IP_ADDR} connectport=16060 connectaddress={WSL_IP_ADDR}
```

5. Connect port 6060 in windows from wsl
```
netsh interface portproxy add v4tov4 listenport=6060 listenaddress={NETWORK_IP_ADDR} connectport=6060 connectaddress={WSL_IP_ADDR}
```

## (Optional) Enabling video/voice call

- Tinode uses STUN/TURN/ICE servers for video/voice call, to enable them, expose the port as well

1. Open cmd as admin in windows

2. Expose the port (default is 3478)
```
netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=3478
```

3. Connect port 3478 in windows from wsl
```
netsh interface portproxy add v4tov4 listenport=3478 listenaddress={NETWORK_IP_ADDR} connectport=3478 connectaddress={WSL_IP_ADDR}
```

4. Lastly, i don't know if this is neccessary or not, but sometimes simply exposing the port on windows won't do it, you also need to expose the port in wsl

- open wsl then use the command below
```
sudo ufw allow 3478/tcp
sudo ufw allow 3478/udp
```