
## Firewall 

```powershell

### Allow WSL2 throuht firewall
New-NetFirewallRule -DisplayName "WSL" -Direction Inbound  -InterfaceAlias "vEthernet (WSL)"  -Action Allow

### Allow Ports
New-NetFirewallRule -DisplayName "ALLOW TCP PORT 80" -Direction inbound -Profile Any -Action Allow -LocalPort 80 -Protocol TCP

New-NetFirewallRule -DisplayName "WHITELIST TCP" -Direction inbound -Profile Any -Action Allow -LocalPort 80,8080,8090 -Protocol TCP

New-NetFirewallRule -DisplayName "ALLOW UDP PORT 53" -Direction inbound -Profile Any -Action Allow -LocalPort 53 -Protocol UDP

New-NetFirewallRule -DisplayName "WHITELIST UDP" -Direction inbound -Profile Any -Action Allow -LocalPort 53,3128 -Protocol UDP
```

