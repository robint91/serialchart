# Sending Data #

_As of version 0.3.2 it is possible to send data to COM ports. This can be done either by using the send box or automatically from configuration file (on run or stop)._

The sent strings supports following escaped characters:

```
\\  ->  \
\n  ->  0x0A  (line end)
\r  ->  0x0D  (line feed)
\t  ->  0x07  (tab) 
\xAB -> byte with hex code AB
\d123 -> byte with decimal code 123
```


# `[_setup_] section ` #


## send\_run ##

### send a string when port is opened (run button clicked) ###

```
send_run = +++\nAT\n
```

## send\_stop ##

### send a string when port is closed (stop button clicked) ###

```
send_stop = exit\n
```