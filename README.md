pushover-cli
==========

pushover-cli is a Python-based command-line client for https://pushover.net to send pushover notifications.  This can be used for a variety
of purposes, including:

* Sending control messages to an old phone running Youtube to start/pause/stop/etc. the playing video remotely.
* Piping low-volume streams to your cellphone (e.g. `tail -f /var/log/my.log | grep ERROR | pushover-cli -`).
  Every line becomes a separate notification and counts against your application's monthly message quota.
* It's essentially an MQTT channel, so anything that can read a pushover message can act on it.
* Tie pushover notifications into your long-running scripts (build, cleanup, upgrade, etc.) to get notices of different system events.

Requirements
------------
* Linux (including WSL) or Mac OSX
* Python 3.7+
* Your own [pushover application token](https://pushover.net/apps/build).
* Your pushover user key, which can be found [here](https://pushover.net).


Download and installation
-------------------------

Download the script, review it, then install it (replace `master` with a commit hash to pin a known version):

```sh
curl -fsSL -o pushover-cli https://raw.githubusercontent.com/markus-perl/pushover-cli/master/pushover-cli
less pushover-cli
sudo install -m 755 pushover-cli /usr/local/bin/pushover-cli
```

`/usr/local/bin` is used because `/usr/bin` is read-only on macOS (System Integrity Protection).

Commandline options
-------------------

```
Usage:   pushover-cli [options] <message> <title>
Stdin:   pushover-cli [options] - <title>
Example: pushover-cli -u ubLBe5u3zNXF9gBtX2zKkezSuPgu3v -t aK5BW3sjAqPsedH44VyQSbaQecoRen "Hello World"

    -u --user     <user id>             Pushover User-ID
    -t --token    <api token>           Pushover API-Token
    -d --device   <device name>         Device Name (if omitted, will broadcast to all devices)
    -p --priority <highest, high, normal, low, lowest>   Default: normal
    -r --retry    <30+>                 Default: 30)
    -e --expire   <30 - 10800>          Default: 300)
    -l --url      <url>                 Link the message to this URL
    -s --sound    <notification sound>  Default: pushover - see https://pushover.net/api#sounds
    -c --config   <path to file>        Default: /etc/pushover-cli.conf, ~/.pushover-cli.conf
    -v --verbose                        Be verbose
    -q --quiet                          Be quiet
    -H --html                           Use HTML output. Requires v2.3 client.
    -M --monospace                      Use monospace font. Requires v3.4 client.
    -m --multiline                      Send all lines read from stdin as a single message.
```

Proxy Utilization
-------------------

Incorporating the PR from acaranta that enabled proxy support, if the `HTTP_ENV` environment variable is 
pointed at a proxy server URL, `pushover-cli` will treat that as the proxy gateway and route the message
to Pushover through it.


Config file
-------------------

Every command line option can also be set by creating the config file ~/.pushover-cli.conf or /etc/pushover-cli.conf.

If both exist, both are read and values in ~/.pushover-cli.conf override those in /etc/pushover-cli.conf.
Command line options always override config file values. `-c <file>` reads only that file instead of the defaults.

The config file contains your private user key and application token, so restrict its permissions:

```sh
chmod 600 ~/.pushover-cli.conf
```

Prefer the config file over `-u`/`-t`: command line arguments are visible to other users in `ps` and are saved in your shell history.

Example file:

```
user=<Pushover User ID>
token=<Pushover Application API Token>
priority=normal
verbose=0
quiet=0
```

After creating this file it is no more necessary to specify these options in the command line which makes it more easier to send a message:
```sh
$ pushover-cli "My Message" "My Title"
```

or

```sh
$ pushover-cli -d 'my_phone' 'My Message' 'My Title'
```

or

```sh
$ pushover-cli -s 'none' 'My Message' 'My Title'
```
for silent notification or use other tones https://pushover.net/api#sounds
