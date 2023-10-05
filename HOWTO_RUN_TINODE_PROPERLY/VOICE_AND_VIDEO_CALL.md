### Enabling Video Calls

Video calls use [WebRTC](https://en.wikipedia.org/wiki/WebRTC). WebRTC is a peer to peer protocol: once the call is established, the client applications exchange data directly. Direct data exchange is efficient but creates a problem when the parties are not accessible from the internet. WebRTC solves it by means of [ICE](https://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment) servers  which implement protocols [TURN(S)](https://en.wikipedia.org/wiki/Traversal_Using_Relays_around_NAT) and [STUN](https://en.wikipedia.org/wiki/STUN) as fallback.

Tinode does not provide ICE servers out of the box. You must install and configure (or purchase) your own servers otherwise video and voice calling will not be available.

Once you obtain the ICE TURN/STUN configuration from your service provider, add it to `tinode.conf` section `"webrtc"` - `"ice_servers"` (or `"ice_servers_file"`). Also change `"webrtc"` - `"enabled"` to `true`. An example configuration is provided in the `tinode.conf` for illustration only. IT WILL NOT FUNCTION because it uses dummy values instead of actual server addresses.

You may find this information useful for choosing the servers: https://gist.github.com/yetithefoot/7592580

### How to Setup STUN/TURN Server

1. install coturn
```
sudo apt-get update
sudo apt-get install coturn
```

2. To let it auto-start at system boot time, modify /etc/default/coturn file.
```
sudo nano /etc/default/coturn
```

3. Find the following line and uncomment it by removing the # to run Coturn as an automatic system service daemon.
```
#TURNSERVER_ENABLED=1
```

4. Save and close the file. And you can then start the Coturn service by running
```
systemctl start coturn
```

5. Then go to `/etc/turnserver.conf` and open or create the file and paste the following content. Replace USERNAME, PASSWORD and PUBLIC_IP_ADDRESS values with your respective ones. You can obtain your PUBLIC_IP_ADDRESS using `ifconfig` in ubuntu
```
fingerprint
user=<USERNAME>:<PASSWORD>
lt-cred-mech
realm=kurento.org
log-file=/var/log/turnserver/turnserver.log
simple-log
external-ip=<PUBLIC_IP_ADDRESS>
```

6. Restart the service
```
sudo service coturn restart
```

7. Test it, go to https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/ and fill in your `/etc/turnserver.conf` details
```
STUN or TURN URI: turn:<PUBLIC_IP_ADDRESS>:3478
TURN username: <USERNAME>
TURN password: <PASSWORD>
```

8. Then press the “Gather candidates” button, if everything is correctly configured, you should see "Done".