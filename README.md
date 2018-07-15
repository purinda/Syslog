# Syslog

An Arduino library for logging to Syslog server via `UDP` protocol in
[IETF (RFC 5424)] and [BSD (RFC 3164)] message format

[![Build Status](https://travis-ci.org/No3x/Syslog.svg?branch=master)](https://travis-ci.org/No3x/Syslog) [![Join the chat at https://gitter.im/arcao/Syslog](https://badges.gitter.im/arcao/Syslog.svg)](https://gitter.im/arcao/Syslog?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

How to use, see [examples].

## Fork
This is a fork of brokentoaster/Syslog which was forked from arcao/Syslog.
It was forked to:
- Fix crash due serial print debug messages (added trough brokentoaster/Syslog)
- Add a simple cache for messages while esp is offline. When online again it sends all the messages. (the implementation is really bad but works for me)
Because for this cache mechanism the SPIFFS is needed. This is not supported by all platforms but by esp8266. Furthermore there is no generic interface to write to a file regardless of SD or SPIFFS.

I have tested with a syslog server from a docker image:
```
docker run -dit --restart unless-stopped -e SYSLOG_USERNAME=admin -e SYSLOG_PASSWORD=1234 -p 65500:80 -p 65501:514/udp pbertera/syslogserver
```
`65501` is the syslog port and `65500` is a webclient to view the syslog.
So in the code set
```
#define SYSLOG_SERVER "example.com"
#define SYSLOG_PORT 65501
```
To check if there is a internet connection the syslog server is connected on port 80. Or if no server-url (but IP) provided google.com is used.
It checks connection status for each message currently.

## Features
 - Supports original Syslog severity level and facility constants
 - Supports both Syslog messge formats: [IETF (RFC 5424)] and [BSD (RFC 3164)]
 - Supports `printf`-like formatting via `logf` methods (use `vsnprintf` method
   inside)
 - Supports fluent interface, see [AdvancedLogging] example
 - Allows to ignore sending specified severity levels with `logMask` function,
   see [AdvancedLogging] example
 - Independent on underlying network hardware. The network hardware library has
   to implement methods of `UDP` astract class only.

## Compatible Hardware
The library uses the Arduino UDP Network API (`UDP` class) for interacting with
the underlying network hardware. This means it Just Works with a growing number
of boards and shields, including:

 - ESP8266 / ESP32
 - ~~Arduino Ethernet~~
 - ~~Arduino Ethernet Shield~~
 - ~~Arduino YUN – use the included `BridgeUDP` in place of `EthernetUDP`, and~~
   ~~be sure to call a `Bridge.begin()` first~~
 - ~~Arduino WiFi Shield~~
 - ~~Intel Galileo/Edison~~
 - ~~Arduino/Genuino MKR1000~~
 - ~~[Arduino module RTL00(RTL8710AF), F11AMIM13 (RTL8711AM)][RTLDUINO]~~
 - ~~... you tell me!~~

## Syslog message formats
This library supports both Syslog message formats [IETF (RFC 5424)] and
[BSD (RFC 3164)]. The newer **IETF** format is used by default. If you want to use
older "obsolete" **BSD** format, just specify it with `SYSLOG_PROTO_BSD` constant
in a last constructor parameter.

```c
Syslog syslog(udpClient, host, port, device_hostname, app_name, default_priority, SYSLOG_PROTO_BSD);
// or
Syslog syslog(udpClient, ip, port, device_hostname, app_name, default_priority, SYSLOG_PROTO_BSD);
// or
Syslog syslog(udpClient, SYSLOG_PROTO_BSD);
```

## Limitations
 - This library is sending empty timestamp in the syslog messages. For IETF
   format it is `NILVALUE` (char `-`) in `TIMESTAMP` field, for BSD format the
   `TIMESTAMP` field is completely ommited. Syslog server should use a time
   of receiving message in this case. It is OK in most cases. This issue will be
   fixed in some of the next releases.


[IETF (RFC 5424)]: https://tools.ietf.org/html/rfc5424
[BSD (RFC 3164)]: https://tools.ietf.org/html/rfc3164
[examples]: https://github.com/arcao/Syslog/tree/master/examples
[AdvancedLogging]: https://github.com/arcao/Syslog/blob/master/examples/AdvancedLogging/AdvancedLogging.ino
[RTLDUINO]: https://github.com/pvvx/RtlDuino
