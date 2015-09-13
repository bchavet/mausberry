# mausberry

Add the following to `/etc/rc.local`

````sh
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
````
