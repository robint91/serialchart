# Advanced Serial Chart Features #

_Please note that these features are experimental and might not be available in the download compiled version, please get Qt SDK and compile/run the source code from SVN repository. The new modular design allows you to add new type of ports, decoders, and display filters._

Basic data chain in the new framework is as follows:

PORT (RAW DATA FROM SERIAL/USB PORT) -> DECODER(PROCESSES DATA) -> VALUES(displayed on screen/chart)

# `[_setup_] section ` #


## _port_ ##

### Open a usb COM port ###

```
port = COM1
```

see  ConfigurationFileSyntax  for related options


### Open a USB HID device (keyboard/mouse/gamepad) ###

```
port = HID   
vid = 0079
pid = 5d0f
```

_Tip: use an utility like USBDeview http://www.nirsoft.net/utils/usb_devices_view.html to find the vendor and product IDs of a specific device (VID/PID)._


## _decoder_ ##

### Specify CSV decoding (default) ###

```
decoder = csv
```

### Specify binary decoding ###

```
decoder = bin
```

### Specify HDLC-style decoding ###

```
decoder =hdlc
hdlc_esc =7d
hdlc_sep =7e
hdlc_xor =20
```

## _display_ ##

### Display **raw** data coming from port (default) ###

```
display = raw
```

### Display data as a **list** of values ###

```
display = list
display_sep = ,
display_skip_transparent = 1
```

Please note that the separator can be specified in [Percent Encoded](http://en.wikipedia.org/wiki/Percent-encoding) string for example for a TAB separated list use:

```
display = list
display_sep = %09
```

Tip: TAB separated values can be easily Copy & Pasted into an Excel spreadsheet. (In Excel use Paste > Paste Special and select Text as format).

### Display data as HEX string ###

```
display = hex
```

# `[any field] sections` #

### Specify data length/type for BIN/HDLC decodings ###

```
type = byte / sbyte / word / sword / float
```


### Specify data Endianness ###

see http://en.wikipedia.org/wiki/Endianness

```
endian  = msbf / lsbf
```


msbf -> most significant byte first
lsbf -> least significant byte first


### Specify data format for custom display (ex: display = list) ###

```
format = %g / %f / %d / %n / %s / %x
```

%n -> field name

%f -> float, format as -9.9

%g -> float, format as -9.9 or -9.9e+9 whichever is shorter

%d -> decimal integer

%s -> raw data for this value

%x -> hex of raw data

You can combine formats as follows:

```
[MyField]
type = float
precision = 3
format = %n = %f
```

will output for ex:

... , MyField `=` 1.123 , ...




### Set precision of floats in format ###

```
precision = 6   
```


Here is how this formatting is applied (providing source code) :

```
v.replace(QByteArray("%g"),QByteArray::number(packetValues[i].toDouble(),'g',precision));
v.replace(QByteArray("%f"),QByteArray::number(packetValues[i].toDouble(),'f',precision));
v.replace(QByteArray("%d"),QByteArray::number(packetValues[i].toInt(),'f',0));
v.replace(QByteArray("%n"),field.toAscii());
v.replace(QByteArray("%s"),packetParts[i]);
v.replace(QByteArray("%x"),packetParts[i].toHex());
```


### Using `h_origin` and `pitch` field parameters ###

Example:
```
[Field1]
h_origin = 50
pitch = 10
```

Use `h_origin` to specify what horizontal line corresponds to the field value of 0. So , in the example above 0 will be charted 50 pixels below the top border of the chart.

Use `pitch` to specify how many units of field's value correspond to 1 pixel on the chart. So in the example above Field1 = 20 will be charted at 50 - 20 / 10 = 48 pixels below the top border of the chart.

Please note that `h_origin` and `pitch`, just like any other field parameter, can be specified in the `[_default_]` section to apply by default to all fields.

If no `h_origin` or `pitch` is specified then it is calculated automatically based on field's min / max parameters, so that a value of max is always charted on the top-most line of the chart , and min value is charted on the bottom line on the chart.