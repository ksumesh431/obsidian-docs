``` bash
docker run -d \
  --name=openvpn-as --cap-add=NET_ADMIN \
  -p 943:943 -p 443:443 -p 1194:1194/udp \
  -v ~/.openvpn_config:/openvpn \
  openvpn/openvpn-as

```

grep **Auto-generated pass** in the docker logs for pass


for updating pass

sacli --user openvpn --new_pass <PASSWORD> SetLocalPassword
sacli start