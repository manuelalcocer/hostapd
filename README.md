# hostapd

## Crear punto de acceso con 'hostapd'

- Configurar la interfaz wlan en modo AP:
~~~
# iw dev wlan0 interface add wlan0_ap type __ap
~~~

- Cambiar la MAC de la interfaz:
~~~
# ip link set dev wlan0_ap address 12:34:56:78:ab:ce
~~~

## Modo Bridge: cableada <-> wireless:

- Configuración de _/etc/hostapd/hostapd_:
~~~
ssid=YourWiFiName
wpa_passphrase=Somepassphrase
interface=wlan0_ap
bridge=br0
auth_algs=3
channel=7
driver=nl80211
hw_mode=g
logger_stdout=-1
logger_stdout_level=2
max_num_sta=5
rsn_pairwise=CCMP
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP CCMP
~~~

## Modo Router: cableada <-> wireless:

- Configuración de _/etc/hostapd/hostapd_:
~~~
ssid=YourWiFiName
wpa_passphrase=Somepassphrase
interface=wlan0_ap
# Esta línea no debe existir o debe estar comentada
# bridge=br0
auth_algs=3
channel=7
driver=nl80211
hw_mode=g
logger_stdout=-1
logger_stdout_level=2
max_num_sta=5
rsn_pairwise=CCMP
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP CCMP
~~~

- Dar una IP manual al AP

- Crear regla SNAT de iptables:
~~~
# my_net_segment='10.10.0.0/24'
# out_iface='enp8s0'
# iptables -t nat -A POSTROUTING -s ${my_net_segment} -o ${out_iface} -j MASQUERADE
~~~

- [Opcional]: Crear regla DNAT de iptables:
~~~
# my_siface='enp8s0'
# my_sport='80'
# dest_ip='10.10.0.23'
# iptables -t nat -A PREROUTING -p tcp --dport ${my_sport} -i ${my_siface} -j DNAT --to ${dest_ip}
~~~

- Activar bit de ipv4 forward
~~~
# sysctl -w net.ipv4.ip_forward=1
~~~

- Para el cliente hay dos opciones:
    * Crear regla de encaminamiento _default_ en el cliente cuando se conecte
    * Activar un servidor DHCP en el AP

## Conectar con _wpa_supplicant_ a un AP

- Crear la contraseña para el AP:
~~~
# wpa_passphrase SSID passphrase > fichero.conf
~~~

- Conectar al AP:
~~~
# wpa_supplicant -B -i interfaz -c fichero.conf
~~~

## Configurar la interfaz como Ad-Hoc

- Poner en modo Ad-Hoc
~~~
# iw interface set type ibss
# ip link set interface up
~~~

- Crear/Conectar a la red Ad-Hoc
~~~
# iw interface ibss join your_ssid frequency
~~~
