#Describes Configuration file syntax for SerialChart application (.scc files)

# Introduction #

SerialChart configuration is done through a text file. The general format of the file is as follows:
```
[section1]
param1 = value
param2 = value
...

[section2]
param1 = value
param2 = value
```

# Setup Section #

Each configuration file starts with the `[_setup_]` section, here you can setup global parameters. For example:
```
[_setup_]
port=COM3   
baudrate=57600

width=1000
height=201
background_color = white

grid_h_origin = 100
grid_h_step = 10
grid_h_color = #EEE
grid_h_origin_color = #CCC

grid_v_origin = 0
grid_v_step = 10
grid_v_color = #EEE
grid_v_origin_color = transparent
```

Below are the parameters that can be used in the `[_setup_]` section:

### port ###
Specifies the communication port to which the software connects. For ex: COM1,COM2,..

### baudrate ###
Baudrate in bps (bits per second). Only certain values are currently supported: 110,300,600,1200,2400,9600,19200,38400,57600,115200.

_Please note that currently no flow control parameters are supported, and parity will default to "none", stop bits defaults to 1, as these are most common values._

### width, height ###
width and height of the chart area in pixels

### background\_color ###
Background color of the chart.

**Note about color formats**
Please note that colors in .SCC files can be specified in hex format (for ex: #FFF, or #FFFFFF) or named color (white,blue,magenta,pink,gray) see: http://www.w3.org/TR/SVG/types.html#ColorKeywords . Please note the special value _transparent_ which means "no color". You can use it if you don't want some elements to be drawn at all.

### grid\_h\_origin, grid\_v\_origin ###
Both horizontal and vertical grids will have an origin axis line, usually of different color from the regular grid lines. These parameters specifies the shift of this line from top/left borders of chart in pixels.

### grid\_h\_origin\_color, grid\_v\_origin\_color ###
Color of the grid origin (axis) line.

### grid\_h\_step, grid\_v\_step ###
Draw a grid line at each step of pixels from the origin line.

### grid\_h\_color, grid\_v\_color ###
Color of regular grid lines.


# Default and Field Sections #

SerialChart accepts packets in CSV format (other formats might be supported in the future). Each packet comes on a separate line and each field value is separated by comma. Here is an example of sample data that SerialChart would receive:
```
100,0.50,0.70
101,0.30,0.50
102,0.25,0.35
```

The purpose of the following sections in the configuration file is to specify parameters for each field in the packet. For example:
```
[_default_]
min=-1
max=1

[Field1]
color=gray
min=0
max=255

[Field2]
color=blue

[Field3]
color=red
```

You can specify parameters that would apply by default to all fields in the **`[_default_]`** section. These parameters will apply to all field sections unless they will be overridden in the field section by  parameters with the same name.

In the example above note that Field2 and Field3 will inherit min=-1 and max=1 from the `[_default_]` section. However Field1 will override these values with min=0 and max=255

After you have defined the default parameters, you should define one section for each field that will be received in a packet. Field sections should be in the order in which they are received in the packet.

The name of field sections can be chosen randomly but should be distinct from the reserved section names `_setup_`, `_default_`. For that matter avoid any names that start and end with `_`.

Below is an explanation of the parameters accepted in the `[_default_]` and field sections:

### min,max ###
These are the field values that correspond to the top and bottom lines of the chart. In other words if you specify min = -1 and max = 1. A value of -1 will be plotted at the top border of the chart , and a value of 1 will be plotted at the bottom border of the chart. A value of 0 (which is is the midpoint between -1 and 1) will be plotted at the middle of the chart. Field values are linearly mapped from `[min,max]` range to `[0,height]` range where _height_ is  the chart height in pixels, specified in the setup section.

### color ###
This is the color used to plot a field value. Use _transparent_ color value if you don't want a specific field to be plotted.

### dash ###
Creates an interrupted line. For example:
dash = 3
will render 3 samples then will not render the next 3 samples, then render 3 samples, then again pause for 3 samples and so on...