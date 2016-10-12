# Alternate Mausberry Switch Controller

I recently purchased a Mausberry switch for a Raspberry Pi in my car. It works great, but the provided controller script was really hurting performance with its tight GPIO probing loop. I looked around and found a number of alternatives, but they were all much more complicated than I thought was necessary. So, I wrote my own.

My approach is designed to run using cron, which of course means that the Pi will not shut down immediately after the power state changes on the switch. This is fine for my application, because I wanted the Pi to stay on for 15 minutes anyway. It still uses the same probing method as the official script, but when it runs at most once per minute, the performance hit is negligable. Especially compared to probing once per second.

## Installation

### 1. Clone this repo onto your Raspberry Pi

Either copy, move, or symlink the `mausberry` script to `/usr/local/sbin/mausberry`

### 2. Add the following to `/etc/rc.local`

```sh
GPIO_OUT=23
GPIO_IN=24

if [ -f /etc/default/mausberry ]; then
    . /etc/default/mausberry
fi

echo "$GPIO_OUT" > /sys/class/gpio/export
echo "in" > /sys/class/gpio/gpio$GPIO_OUT/direction
echo "$GPIO_IN" > /sys/class/gpio/export
echo "out" > /sys/class/gpio/gpio$GPIO_IN/direction
echo "1" > /sys/class/gpio/gpio$GPIO_IN/value
```

This activates the GPIO pins that are connected to the Mausberry switch.

### 3. Create a cron job to run the mausberry script periodically

I am running mine every minute. Make sure you adjust for the path where the script lives

    * * * * * /usr/local/sbin/mausberry
    
#### 4. Custom settings

Both the script and the pin registration read from `/etc/default/mausberry` where you can override the following environment variables

* *GPIO_OUT* - The GPIO pin connected to the "out" pin on the Mausberry switch. Defaults to 23
* *GPIO_IN* - The GPIO pin connected to the "in" pin on the Mausberry switch. Defaults to 24
* *DELAY* - The number of minutes to wait after detecting a power change before shutting down. Defaults to 0
