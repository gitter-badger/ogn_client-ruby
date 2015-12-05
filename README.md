# ogn_client-ruby

[![Join the chat at https://gitter.im/svoop/ogn_client-ruby](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/svoop/ogn_client-ruby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[OGN](http://glidernet.org) broadcasts aircraft positions as [APRS](http://www.aprs.org)/[APRS-IS](http://www.aprs-is.net) messages. This gem hooks into this stream of data and provides the necessary classes to parse the raw message strings into meaningful objects.

* Author: [Sven Schwyn](http://bitcetera.com)
* Homepage: https://github.com/svoop/ogn_client-ruby

:loudspeaker: A word from the shameless commerce division: Looking for a freelance Ruby developer? Contact Sven on http://bitcetera.com

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'ogn_client-ruby', require: 'ogn_client'
```

And then execute:

    $ bundle

Or install it yourself with:

    $ gem install ogn_client-ruby

## Usage

### Subscribe to OGN

Choose a [valid callsign](http://www.aprs-is.net/Connecting.aspx#loginrules) and [appropriate filters](http://www.aprs-is.net/javAPRSFilter.aspx), then start listening to the broadcasted raw messages:

```ruby
OGNClient::APRS.start(callsign: 'ROCT', filter: 'r/33/-97/200') do |aprs|
  puts aprs.gets until aprs.eof?
end
```

### Parse Raw Message Strings

In the above example, each `aprs.gets` returns a raw message string. To decode this string, just pass it to the message parser:

```ruby
OGNClient::Message.parse(aprs.gets)
```

This factory method will return an instance of one of the following four classes.

:point_up: Refer to the wiki for an introduction to [OGN flavoured APRS](https://github.com/svoop/ogn_client-ruby/wiki) messages.

#### OGNClient::Sender

Senders are usually aircraft equipped with [FLARM](https://flarm.com) (anti-collision warning system) or similar devices which broadcast position data as RF beacons.

The data is converted into the metric system since [OGN](http://glidernet.org) is primarily made for gliders which mostly use the metric system for speed, climb rate and so forth.

Attributes:
* **callsign** - origin callsign
* **receiver** - receiver callsign
* **time** - UTZ/zulu time with date
* **longitude** - degrees from -180 (W) to 180 (E)
* **latitude** - degrees from -90 (S) to 90 (N)
* **altitude** - meters
* **heading** - degrees from 1 to 360
* **speed** - kilometers per hour
* **stealth_mode** - boolean (should always be false)
* **no_tracking** - boolean
* **sender_type** - see [SENDER_TYPES](https://github.com/svoop/ogn_client-ruby/blob/master/lib/ogn_client/messages/sender.rb)
* **address_type** - see [ADDRESS_TYPES](https://github.com/svoop/ogn_client-ruby/blob/master/lib/ogn_client/messages/sender.rb)
* **id** - device ID
* **climb_rate** - meters per second
* **turn_rate** - revolutions per minute
* **signal** - signal to noise ratio in decibel
* **errors** - number of CRC errors
* **frequency_offset** - kilohertz
* **gps_accuracy** - array [vertical meters, horizontal meters]
* **proximity** - array of callsign tails

#### OGNClient::Receiver

Receivers are little RF boxes which pick up the RF beacons from aircraft and relay them to the OGN servers as messages. They send their own status messages on a regular basis.

Attributes:
* **callsign** - origin callsign
* **receiver** - receiver callsign
* **time** - UTZ/zulu time with date
* **longitude** - degrees from -180 (W) to 180 (E)
* **latitude** - degrees from -90 (S) to 90 (N)
* **altitude** - meters
* **heading** - degrees from 1 to 360
* **speed** - kilometers per hour
* **version** - software version "major.minor.patch"
* **platform** - e.g. :arm
* **cpu_load** - as reported by "uptime"
* **cpu_temperature** - in degrees celsius
* **ram_free** - megabytes
* **ram_total** - megabytes
* **ntp_offset** - milliseconds
* **ntp_correction** - parts-per-million
* **signal** - signal-to-noise ratio in decibel

#### OGNClient::Comment

Comments are sent on a regular basis to keep the connection alive.

Attribute:
* **comment** - raw message with the comment marker stripped

#### OGNClient::Message

Generic message objects are only used as a fallback in case the raw message string could not be parsed into one of the above three [OGN](http://glidernet.org) object types. If this happens, either the [OGN](http://glidernet.org) specifications have changed, the message is invalid or you have found a bug in the parser code. You can choose to ignore such messages or to store them for debugging and replaying once the bug is fixed.

## Debugging

To get additional debug messages, either invoke Ruby with the `-d` flag or set the global debug variable explicitly:

```ruby
$DEBUG = true
```

## Development

Check out the repository, install the dependencies and run the test suite:

    $ git clone git@github.com:svoop/ogn_client-ruby.git
    $ bin/setup
    $ rake

If you are on Mac OS X, +guard+, +guard-minitest+ and +minitest-osx+ are also installed and therefore you get the test results as notifications by running a guard watchdog with:

    $ guard

To play around with the gem:

    $ bin/console

And to install this gem onto your local machine:

    $ bundle exec rake install

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/svoop/ogn_client-ruby

## Feature Brainstorming

* configuration option to switch between metric and aeronautical units
* more data sources such as ADS-B

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
