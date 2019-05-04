# Internet Archive - Universal Library project README 

This Ansible role installs the Internet Archive's dweb-mirror project on Internet-in-a-Box (IIAB).

The project is a local server that allows users to browse resources from the Internet Archive stored
on local drives - including USB drives.  

It includes a crawler that can regularly synchronize local collections,
against a list of Internet Archive items and collections, and those collections can be moved between IIAB systems.

When connected to the internet, the server works as a Proxy, i.e. it will store Internet Archive content the user views for later off-line viewing. 

There are components to integrate the IA server with decentralized tools including IPFS, WebTorrent, GUN, WOLK, 
both for fetching content and for serving it back to the net or locally. 

This is an ongoing project, continually adding support for new Internet Archive content types; new platforms; and new decentralized transports.

## Using it

### Starting server
(Not required on IIAB)
```
cd <wherever>/dweb-mirror && ./internetarchive --server &
```
The startup is a little slow but you'll see some debugging when its live

### Browsing

If you are working directly on the machine (e.g. its your Mac) then
browse to [http://localhost:4244] which will open the UI in the browser and it should see the Archive UI.

If you are remote from the machine, then browser to: `http://<IP of machine>:4244`

On IIAB the server can be accessed at [http://box.net:4244] (try [http://box.local:4244] if that doesn't work)

If you don’t get a Archive UI then look at the server log (in browser console) to see for any “FAILING” log lines which indicate a problem. 

Expect to see errors in the Browser log for `http://localhost:5001/api/v0/version?stream-channels=true` which is checking for a local IPFS server which is not started here.

Expect, on slower machines or slower network connections, to see no images the first time, refresh after a little while and most should appear. 

## Administration

Administration is carried out through the same User Interface as browsing. 

Access [http://box.net:4244/local] to see a display of local content, this interface is under development and various admin tools will be added here. *at some point this will become the default page*.

Access [http://box.net:4244] to get the Internet Archive main interface if connected to the net. 

While viewing an item or collection, 
the "Crawl" button in the top bar indicates whether the item is being crawled or not. 
Clicking it will cycle through three levels:
* No crawling
* Details - sufficient information will be crawled to display the page, 
for a collection this also means getting the thumbnails and metadata for the top items. 
* Full - crawls everything on the item, this can be a LOT of data, including full size videos etc, so use with care if bandwidth/disk is limited.

### Disks
The server checks for disks in all the likely places, the list of places it checks, in an unmodified installation can be seen at 
`https://github.com/internetarchive/dweb-mirror/blob/master/configDefaults.yaml#L7`

You can override this in `/root/dweb-mirror.config.yaml` (see below)

Items are stored in subdirectories of the first of these directories found, but read from any of the locations. 

If you disk space is getting full, its perfectly safe to delete any subdirectories, 
except the `.hashstore` at the top level of each,
the server will refetch what it needs if you browse to the item again when connected to the internet. 

### Maintenance
If you are worried about corruption, or after for example hand-editing or moving cached items around. 
```
# Run everything as root
sudo sh
# cd into location for your installation - which varies between platforms
cd /opt/iiab/internetarchive/node_modules/@internetarchive/dweb-mirror || cd /usr/local/node_modules/@internetarchive/dweb-mirror
./internetarchive -m
```
This will usually take about 5-10 minutes depending on the amount of material cached, 
just to rebuild a table of checksums.

### Advanced
Most functionality of the tool is controlled by two YAML files, 
the second of which you can edit if you have access to the shell. 

You can view the current configuration by going to [http://box.lan:4244/info] or [http://localhost:4244/info] depending on how you are connected.

The default, and user configurations are displayed as the `0` and `1` item in the `/info` call. 

In the Repo is a [default YAML file](https://github.com/internetarchive/dweb-mirror/blob/master/configDefaults.yaml) which is commented. 
It would be a bad idea to edit this, so I'm not going to tell you where it is on your installation! 
But anything from this file can be overridden by lines in `/root/dweb-mirror.config.yaml`. 
TODO Note this file will probably move location. 
Make sure you understand how yaml works before editing this file, 
if you break it, you can copy a new default from [dweb-mirror.config.yaml on the repo](https://github.com/internetarchive/dweb-mirror/blob/master/configDefaults.yaml#L7)

Note that this file is also edited automatically when the Crawl button described above is clicked. 

As the project develops, this file will be editable via a UI. 

## Installation
Dweb-Mirror is installable on most Linux based systems. Specific installation instructions are available for: 
 [RACHEL 3+ from World Possible](INSTALLATION-rachel.md); [Internet In A Box on Raspberry Pi](INSTALLATION-iiab-raspberry.md); and [directly on Raspberry PI](INSTALLATION-raspberrypi.md). 
 
For anything else (incuding MacOSX), please follow [generic instructions](INSTALLATION.md) and please let us know how it went.

## Update
Dweb-mirror is under rapid development, as is the Javascript UI. Its recommended to update frequently. 

From a Terminal window
```
sudo sh # Run all commands as root
cd /opt/iiab/internetarchive
yarn upgrade  # Currently this can take up to about 20 minutes to run, we hope to reduce that time
```

## Crawling
The Crawler will be built into the UI fairly soon, for now it has to be run in a terminal window.

Its highly configurable either through the YAML file described above, or from the command line.

In a shell 
```
# Run all commands as root from dweb-mirror's directory
sudo sh

# cd into location for your installation - which varies between platforms
cd /opt/iiab/internetarchive/node_modules/@internetarchive/dweb-mirror || cd /usr/local/node_modules/@internetarchive/dweb-mirror

# To get a full list of possible arguments
./internetarchive --help

# Perform a standard crawl
./internetarchive --crawl 

# To fetch the "foobar" item from IA. 
./internetarchive --crawl foobar 

# To crawl top 10 items in the prelinger collection sufficiently to display and put 
# them on a disk plugged into the /media/pi/xyz
# TODO check where pi actually put them. 
./internetarchive --copydirectory /media/pi/xyz/archiveorg --crawl --rows 10 --level details prelinger
```
## Troubleshooting
There are two logs of relevance, the browser and the server.

**Browser**: If using Chrome then this is at View / Developer Tools / Javascript Console or something similar.

**Server**: 
On IIAB from a Terminal window. 
```
journalctl -u internetarchive
```
TODO find log files on other platforms

## Known Issues
See [github dweb-mirror issues](https://github.com/internetarchive/dweb-mirror/issues); and [github dweb-archive issues](https://github.com/internetarchive/dweb-archive/issues);

## More info

Dweb-Mirror Lives on github at:
[dweb-mirror](https://github.com/internetarchive/dweb-mirror);
[source](https://github.com/internetarchive/dweb-mirror);
[issues](https://github.com/internetarchive/dweb-mirror/issues);
[API.md](./API.md) API documentation for dweb-mirror

This project is part of our larger Dweb project, see also: 
[dweb-universal](https://github.com/internetarchive/dweb-universal) info about others distributing the web;
[dweb-transport](https://github.com/internetarchive/dweb-transport) miscellaneous inc GUN gateway and webtorrent;
[dweb-objects](https://github.com/internetarchive/dweb-objects) library of dweb objects;
[dweb-archive](https://github.com/internetarchive/dweb-archive) archive UI in Javascript;
[dweb-archivecontroller](https://github.com/internetarchive/dweb-archive) Knows about the structure of archive objects;

