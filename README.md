# volatility-filevault2
This is a volatility plugin which attempts to extract Apple FileVault 2 Volume Master Keys.

Writeup is here: https://tribalchicken.net/extracting-filevault-2-keys-with-volatility/

I have tested with OS X:
- 10.9 
- 10.10
- 10.11
- 10.12 (macOS)

NOTE: Has not (yet) been tested on 32-bit OS X.

## How it works
Filevault appears to keep the volume master key in a consistently identifiable region of read only kernel memory. The method used by the plugin is very simple, as all the information is already exposed by Volatility.

In a nutshell, the plugin searches through the memory map of kernel space, searching for a string that is aligned on a map boundary and repeated exactly 0x430 bytes later.

Thats all there is to it (The writeup linked above has further information).

## Usage
filevault2.py is a plugin for the Volatility Framework. You can either place the plugin in the plugins directory at `volatility/plugins`, or  alternatively, you can place the plugin in a separate directory and point volatility to it with `--plugins`

For example, using a directory called "Plugins":

```
voldev$ ls plugins
fielvault2.py
voldev$ volatility --plugins=plugins/ --profile=MacElCapitan_10_11_6_15G1004 -f osx10_11_6.vmem mac_filevault2
```

## Common Problems

### Volatility tells you it needs something to do

Volatility doesn't know about the plugin. Check the location of the plugin, and run `volatility --info` to determine if it is detected

## "No suitable address space mapping found"

This isn't an issue with the plugin, but either the profile selected does not match the memory image or the image is in a format that is not understood. Ensure you have the correct profile selected (Keep in mind in some cases there can be different builds of the same OS X version)

### The plugin doesn't find anything
There could be many causes.

- The drive is not FileVault protected.
- The memory image does not contain the key (Image captured after key is evicted from memory, overwritten during acquisition, etc)
- The key exists but the plugin doesn't find it.

If you suspect the plugin isn't working for you then I would love to know.
