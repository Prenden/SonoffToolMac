# Sonoff Basic 3 Firmware flash

## First steps

Install without OTA jumper.

After installed power on => firmware upgrade with EWLink app (I used the SonoffDiy wifi).

After successful firmware update (3.3.0), power off, add OTA jumper

## The router thingy

Power on sonoffDiy router.

```
sonoffDiy
20170618sn
```

Wait device for connect.

Find devices:
```
dns-sd -B _ewelink._tcp
```

Get device IP:
```
dns-sd -q eWeLink_1000902b03.local

```

## Postman actions after we have the IP

### Get the information

```
POST http://<ip>:8081/zeroconf/info
Headers: {
  Content-Type: application/json,
}
Body {
  "deviceid": "<deviceId>",
  "data": {}
}
```

### OTA unlock

```
POST http://<ip>:8081/zeroconf/ota_unlock
Headers: {
  Content-Type: application/json,
}
Body {
  "deviceid": "<deviceId>",
  "data": {}
}
```

We have successfully unlocked OTA.

## Webserver

Create a folder which contains `sonoff-basic.bin`.

### Get SHA sum of the file

```
shasum -a 256 sonoff-basic.bin
```

### Install webserver:

```
npm i -g http-server
```

### Start webserver:

In the previously created folder run:

```
http-server
```

## The flashing

And the final part!

```
POST http://<ip>:8081/zeroconf/ota_flash
Headers: {
  Content-Type: application/json,
}
Body {
  "deviceid": "<deviceId>",
  "data": {
    "downloadUrl": "http://<webserver-ip>:8080/sonoff-basic.bin",
    "sha256sum": "<sha-sum>"
  }
}
```

Wait until you see the 404 error on your webserver log.

## Connecting

After some seconds a new SSID will appear. Something like: `sonoff-xxxx`.
Connect to that and wait for the captive portal.

Setup your WIFI details (2.4Ghz).

Wait a few seconds and the SSID will disappear.

Connect to your WIFI network.

Find the IP address of the newly connected device. And paste that to the browser.

## Postinstall

We should upload the normal firmware: `sonoff.bin`.
Go to `Upgrade firmware`, upload that as a file.
Wait for restart.

Congratulation you have successfully installed your new Sonoff DIY device.
