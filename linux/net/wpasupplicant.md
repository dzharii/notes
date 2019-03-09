```sh
sudo apt-get install net-tools -y
```


```sh

sudo apt-get install wpasupplicant -y
sudo wpa_passphrase your-ESSID your-passphrase | sudo tee /etc/wpa_supplicant.conf
cat /etc/wpa_supplicant.conf

sudo systemctl daemon-reload
sudo systemctl restart dhcpcd

sudo systemctl restart wpa_supplicant
```