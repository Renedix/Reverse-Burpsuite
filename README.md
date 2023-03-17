# Reverse-Burpsuite
Using Docker-Compose, configures a reverse proxy (NGINX) to running through a Burpsuite HTTP Listener.

# Purpose

Occasionally during application security assessments we find configuration pages that setup integrations with other applications. These integration points are sometimes vulnerable, and it is theoretically possible that the responses from these endpoints also have vulnerabilities.

This tool was designed to help understand application integrations. It allows a tester to point redirect the integration to a device they control. This tool uses NGINX as a reverse proxy and tunnels all requests through the BurpSuite security tool. This provides a way tamper with the requests used by applications on the back channel in real time.

![image](https://user-images.githubusercontent.com/17691342/226031238-ae71541c-6b45-4d1c-84e4-9b327970dce4.png)

# SETUP

## 1) CONFIGURE NGINX
### Path: 
```dir ./containers/nginx/nginx/config.d/```

WARNING: This image was only designed and tested with a single config file.

This directory holds the proxy config file used by nginx. The contents of target.conf should be updated to direct the request to your target host.

Update the following lines, replacing "www.google.com" with your target host:

proxy_set_header Host
proxy_set_header Referer

## 2) CONFIGURE BURPSUITE PUBLIC KEY AUTHENTICATION authorized_keys
### Path: 

```
dir ./containers/burpsuite/config/
```

SSH was enabled with public key authentication for the burpsuite user. This is used to connect to the BurpSuite client over X11. A key pair must be generated, with the public key placed in an authorized_keys file in the config director>

To generate a keypair:

``` 
ssh-keygen -t rsa 
```

Place the public key to the config directory:

```
cat id_rsa.pub > ./containers/burpsuite/config/authorized_keys
```

## 3) CONNECT TO BURPSUITE

WARNING: This was only tested using the debian based system Kali.

The burpsuite HTTP service is not active after provisioning the environment. This needs to be configured.

To run burpsuite over X11, from a debian system, run:

```
ssh -o "StrictHostKeyChecking=no" -X -i id_rsa burpsuite@localhost "/bin/sh burpsuite.sh"
```

localhost:
        Replace with your host
id_rsa:
        Replace with your private key generated above

## 4) CONFIGURE PROXY

Burpsuite needs to be configured as an invisible HTTP proxy. To do this, you need to edit the OPTIONS tab of Proxy tab.

![image](https://user-images.githubusercontent.com/17691342/226022977-48864e0b-ce1d-4094-bae4-22b0e8fe5268.png)

Edit the 127.0.0.1:8080 Proxy Listeners

### Binding Tab:
Bind to Port:
        443
Bind to Address:
        All Addresses

![image](https://user-images.githubusercontent.com/17691342/226024142-920fa08a-fe0e-412a-a3b4-78db199c75eb.png)


### Request Handling Tab:

Redirect to host:
        your target host
enable "force to https"
enable "Support invisible proxying"

![image](https://user-images.githubusercontent.com/17691342/226024747-54c57866-2785-48ff-aa33-2122ce3e0bba.png)


## 5) TEST

### Request

Make a HTTP (80) request to environment host using a web client. 

![image](https://user-images.githubusercontent.com/17691342/226025119-2f7f4167-c1a1-4787-8885-c237ba4783df.png)

### Intercept

All requests should now be redirected through the burpsuite proxy.

![image](https://user-images.githubusercontent.com/17691342/226025542-969ddd72-a658-4633-a264-2f27539501f6.png)
