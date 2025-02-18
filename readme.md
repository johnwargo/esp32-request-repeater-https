# ESP32 Request Repeater (HTTPS)

A simple Arduino sketch for ESP32 that makes a TLS (HTTPS) request to a supplied host then puts the device to sleep for a specified period before doing it again. 

This is the HTTPS version of the sketch published in [ESP32 Request Repeater (HTTP)](https://github.com/johnwargo/esp32-request-repeater-http) and described in [Putting an ESP32 Device to Sleep](https://johnwargo.com/posts/2025/esp32-sleep/).

## Background

I wanted to learn how to use the power management capabilities of the ESP32 processor and this is the sketch I created to help me learn. I'd been doing a lot of work sending HTTP and HTTPS requests from an ESP32 device, so that made me think of creating a periodic requester sketch.

The ideas is that there's some device that needs to phone home periodically; connect to a remote host using HTTPS and send some data to the server before shutting down.

## Configuration

To configure the sketch, copy the repository's `config.h.rename` to `config.h` (in the same folder) then update the contents of the new file with the settings for your particular environment.

This sketch uses the [Bacon Ipsum JSON API](https://baconipsum.com/json-api/). 

```c
#define REMOTE_HOST "https://baconipsum.com/api/?type=meat-and-filler&paras=1"
#define SLEEP_DURATION_MINUTES 1.5
#define WIFI_SSID "MyLocalNetwork"
#define WIFI_PASSWORD "my long and complicated wi-fi password"
#define WIFI_CONNECT_LIMIT 30000  //milliseconds
```

| Config Setting         | Description |
| ---------------------- | ----------- |
| `REMOTE_HOST`            | The url for the system you want the sketch to trigger every time it runs. Add any query string parameters you need as well (optional). |
| `SLEEP_DURATION_MINUTES` | The duration the device sleeps between requests. <br/>**Note:** Because the sketch wakes up, makes the request, then shuts down so quickly, that I wasn't able to upload sketch updates to the device. To resolve this, I added a 30 second wait at the beginning of every wake period to give me time to upload sketch updates. Be sure to subtract this 30 seconds from the value you configure for this property. |
| `WIFI_SSID`            | The Wi-Fi network name (SSID) you want the device running the sketch to connect to. |
| `WIFI_PASSWORD`        | The password for the Wi-Fi network SSID specified in `WIFI_SSID` |
| `WIFI_CONNECT_LIMIT` | Specifies how many milliseconds the sketch should wait before abandoning connecting to the Wi-Fi network. |

Once you've populated the `config.h` file, save your changes then build and deploy the sketch to an ESP32 device.

The repository contains the Certificate Authority certificate for the Bacon Ipsum site; if you connect to a different host, you must generate the certificate file used by the sketch using the instructions found in [Automated Public Cert to Arduino Header Conversion](https://johnwargo.com/posts/2025/public-cert-arduino/).

## Operation

When the sketch runs, it:

1. Tells you how many times it's woken and made (or tried to make) a request
2. Waits for 30 seconds so you can upload sketch updates to the device
3. Connects to the Wi-Fi network
4. Sends the request, displaying the results on the Serial Monitor
5. Puts the device to sleep for `SLEEP_DURATION_MINUTES`

Here's an example of the sketch's output to the Serial Monitor:

```text
**********************************
* ESP32 Request Repeater (HTTPS) *
**********************************
Wakeup Reason: Not caused by exit from deep sleep
Waiting 30 seconds to allow for sketch uploads

Connecting to MyLocalNetwork
...
WiFi connected
IP address: 
192.168.86.63
Connecting to https://baconipsum.com/api/?type=meat-and-filler&paras=1
Response: 200
["Aliqua ullamco shankle tempor cupidatat qui strip steak turducken veniam sint irure consequat alcatra minim est.  Ut brisket strip steak turducken, tenderloin nostrud sint elit.  Ipsum dolore non ham hock.  Exercitation brisket commodo veniam ham hock pancetta ullamco."]
```

**Note:** If the device cannot connect to the Wi-Fi network for `WIFI_CONNECT_LIMIT` milliseconds, the sketch puts the device to sleep hoping that when it wakes up again the Wi-Fi network connection will succeed.

***

If this code helps you, please consider buying me a coffee.

<a href="https://www.buymeacoffee.com/johnwargo" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>
