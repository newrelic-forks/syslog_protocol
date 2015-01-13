# Syslog protocol

Forked from https://github.com/eric/syslog_protocol. The original version was designed to be rfc 3164 compliant. This forked version makes minor changes to make it Rsyslog compatible so it will work better with the Heka Rsyslog Decoder.

## Examples

### Manipulate packets manually

    require 'syslog_protocol'

    p = SyslogProtocol::Packet.new
    p.hostname = "space_station"
    p.facility = "kern"
    p.severity = "warn"
    p.tag = "test"
    p.content = "flight control broken"
    p.to_s
    # => "<4>2015-01-13T18:44:42+00:00 space_station test: flight control broken\n"
    p.pri
    # => 4
    p.facility
    # => 0
    p.facility_name
    # => "kern"
    p.severity_name
    # => "warn"
    p.warn?
    # => true
    p.info?
    # => false


### Use a Logger to generate packets

    require 'syslog_protocol'

    logger = SyslogProtocol::Logger.new("space_station", "uucp", "user")
    logger.debug("looking for uucp on board the space station.")
    # => "<15>2015-01-13T18:48:06+00:00 space_station uucp: looking for uucp on board the space station.\n"
    logger.emerg("We can't find uucp on the space station!")
    # => "<8>2015-01-13T18:48:38+00:00 space_station uucp: We can't find uucp on the space station!\n"


### Parse packets

    require 'syslog_protocol'

    p = SyslogProtocol.parse("<34>2015-01-13T18:48:38+00:00 space_station space is really getting to me")
    p.facility
    # => 4
    p.severity_name
    # => "crit"
    p.time
    # => 2015-01-13 18:50:25 +0000
    p.content
    # => "space is really getting to me"


### It yells at you for trying to abuse the protocol

    p = SyslogProtocol::Packet.new
    p.facility = 34534534
    # => ArgumentError: Facility must be within 0-23
    p.hostname = "my host"
    # => ArgumentError: Hostname may not contain spaces
    p.hostname = "h\000stname"
    # => ArgumentError: Hostname may only contain ASCII characters 33-126
    # ...etc.
    # It will also unintelligently truncate messages > 1023 bytes so beware.


The MIT License (MIT)

Copyright (c) 2013 Jake Douglas and Eric Lindvall

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
