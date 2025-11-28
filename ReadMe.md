# How to create a VLESS (REALITY) VPN transport with XHTTP via 3X-UI
___
XHTTP is a modern transport (data transmission method) for the VLESS protocol, which allows data to be transmitted using standard HTTP requests. A key feature of this technology is the ability to use CDN (Content Delivery Network) servers between the client and the server. In this tutorial, I will show how you can create your own VPN using the 3x-ui panel and the XHTTP technology.

### VPS/VDS for panel
___
We will need a VPS server to install the panel.

### System requirements
___
- 1 CPU
- 1 GB Ram
- 10 GB Hardware
- OC Ubuntu 24x64
### Setup
___
The first thing to do is to change the server password that arrived in the email. You can use password generators for this or come up with your own password. I do not recommend using your own passwords because, as a rule, they are unreliable. It is best to generate a random set of characters using the command:
```sh
openssl rand -base64 32
```

___
Enter your password, repeat the password entry, and press Enter. To verify that everything is working correctly, without closing the current terminal tab, open a new tab and connect to the server. If the connection is successful after entering the new password, then everything is fine, you can continue. Don't forget to save the password in a safe place.
```sh
passwd
```
___
Next, we update the repository lists and installed packages. If any windows appear during the process, just press Enter:
```sh
sudo apt update
sudo apt upgrade -y
```
___
And restart the server:
```sh
sudo reboot
```
___
### Installing 3x-ui and creating a self-signed TLS certificate
First, you need to install the 3x-ui panel. You can do this using this command:
```sh
bash <(curl -Ls https://raw.githubusercontent.com/mhsanaei/3x-ui/master/install.sh)
```
During the installation process, the script will ask if you want to set your own port: 'Would you like to customize the Panel Port settings? (If not, a random port will be applied) [y/n]:'. You can press 'y' and enter the port you want, but I recommend simply pressing Enter, and the port and all other settings will be set randomly. After that, you will receive the address and login/password for accessing the panel. Save this data in a reliable place.

Enter it into your browser and log in to the panel. A red banner informs us that the connection is not encrypted, and we need to create a TLS certificate. This can be done in two ways: connect a domain and issue a certificate via the x-ui menu, or create a self-signed certificate. We will create a self-signed certificate.

```sh
mkdir cert
cd cert
wget -qO- https://raw.githubusercontent.com/ServerTechnologies/3x-ui-with-xhttp/refs/heads/main/opensslcert | bash
```
After the script runs, you will see several lines with the addresses of the certificate files. Open the panel, in the left menu select Settings → Certificates. Paste the paths to the files into the corresponding fields. Click 'Save' and 'Restart Panel'. The panel will restart and you will see a security warning. This is fine — the browser is complaining because we are using a self-signed certificate. Just click the corresponding button and connect to the panel.

When subsequently entering the panel address in the browser, do not forget to change the protocol from `http` on `https`
___
### Creating a connection. Simple method. XHTTP Vless Reality
XHTTP technology can be used both in conjunction with CDN services and separately — much like a regular Vless Reality with TLS. To do this in the 3x-ui panel, in the right menu select Inbounds → Add Inbound:
- Protocol: Vless
- Port: 443
- Transport: XHTTP
- Security: Reality
- In Dest and SNI, specify any unblocked foreign website
- Click Get New Cert and Get New Seed
- Click 'Create' at the bottom

The connection has been created. One client has also been created. You can add a few more clients. Now, transfer the connection link to your device or scan the QR code — and you can start using it.
___
### Creating a connection. XHTTP via CDN
A CDN (Content Delivery Network) is a network of servers distributed across different regions that helps deliver content to users faster and more reliably. In the context of XHTTP, it is simply an intermediate server between the client and the server where the panel is installed. This slightly slows down the speed, but it allows you to hide your server's IP address behind the CDN server's IP address.

You can use almost any CDN service, but Cloudflare currently provides the ability to use its infrastructure for free, while other services cost money. Therefore, using Cloudflare as an example, I will show you how to create such a connection.

We will need a domain — you can buy it directly in Cloudflare. This domain must be pointed to your VPN server.

Connection settings in 3x-ui:
- Create inbound
- Protocol: Vless
- Port: any, except 80, 443, 22
- Transport: XHTTP
- Host: add the domain
- Path: anything
- Security: empty
- Sniffing: disabled

# Settings in Cloudflare:
- In the left menu, SSL/TLS → select Flexible
- In the left menu, SSL/TLS → Edge Certificates: disable TLS 1.3
- In the left menu, Network: enable gRPC
- In the left menu, Rules → Overview:
  - Create rule button → Origin rule
  - Rule name: anything
  - Field 'When incoming requests match…':
    - Field: Hostname
    - Operator: equals
    - Value: paste your domain or subdomain
  - Destination Port: select 'Rewrite to…' and insert the port from the inbound there
# Client setup:
- Add the key as usual
- Change the address to our domain/subdomain
- Port: 443
- Security: TLS
- SNI: our domain/subdomain
- Save
___
### Clients for different operating systems

Since this technology is relatively new, not all clients support it yet. In this list are the clients that definitely support this transport.

**Windows и Linux**:
- [v2rayN](https://github.com/2dust/v2rayN)

**Android**:
- [v2rayNG](https://github.com/2dust/v2rayNG)

**iOS & macOS arm64**:
- [Streisand](https://apps.apple.com/app/streisand/id6450534064)
