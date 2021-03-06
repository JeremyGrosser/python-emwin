noaaport
--------
This is a native python module that reads a socket connected to an
NOAAPORT or EMWIN server and parses the stream into usable Packets
that can be assembled into files using the included FileAssembler.

Usage
-----
```
$ git clone https://github.com/JeremyGrosser/noaaport.git
$ cd noaaport/
$ python setup.py install
$ noaaport-pipe --help
usage: noaaport-pipe [-h] [--server SERVER [SERVER ...]]
                     [--protocol {nbs,emwin}] [--pattern PATTERN]
                     [--output OUTPUT]
                     [--log-level {DEBUG,INFO,WARNING,ERROR,CRITICAL}]
                     [--delimiter DELIMITER]

optional arguments:
  -h, --help            show this help message and exit
  --server SERVER [SERVER ...]
                        hostname:port
  --protocol {nbs,emwin}
  --pattern PATTERN     regex to filter filenames from the feed
  --output OUTPUT       write output to a directory rather than stdout
  --log-level {DEBUG,INFO,WARNING,ERROR,CRITICAL}
  --delimiter DELIMITER
                        Delimiter to use between files when writing to stdout
```

Examples
--------
All notices and warnings, but not test messages, printed to stdout
```
noaaport-pipe --pattern '^...._(N(^T)|W)'
```

Aviation related messages, with filenames on stderr
```
noaaport-pipe --pattern '^...._(S[AP]|F[ARTCU]|W[ACSVO])' --log-level=INFO
```

Write seismic data to files, with filenames on stderr
```
noaaport-pipe --pattern '^...._SE' --output=SEISMIC/ --log-level=INFO
```

EMWIN radar images to files
```
noaaport-pipe --pattern '^RAD' --output=RADAR/ --server 1.pool.iemwin.net:2211 --protocol emwin
```

All messages from KSEW, KSEA, or KMTR to files
```
noaaport-pipe --pattern '^(KSE[WA]|KMTR)_' --output=KSEW/
```

Using it in a script
--------------------
```python
import noaaport.pipe

# Create a new pipe using the default server and protocol
p = noaaport.pipe.Pipe()

# Iterate through files received from the noaaport server
for filename, content, zip_filename in p:
	# If the filename ends with .ZIS, it's automatically unpacked and returned
	# as separate files. zip_filename is set to the name inside the zip archive.
	# zip_filename is always None when using the nbs protocol
	print filename
	print content
```

References
----------
http://www.wmo.int/pages/prog/www/ois/Operational_Information/Publications/WMO_386/AHLsymbols/TableB1.html  
http://www.weather.gov/emwin/winpro.htm  
http://www.inoaaport.net/  
http://www.wxmesg.com/statusbyteservers.asp  
