# ToolBoxC64

Here, you can find some tools in assembler 6502 for Commodore 64.

## memtools.a 

This library, contains some routine to make copy in memory. 

### memCopy

**memCopy** *source,dest,count*

- source (16bits) 
- dest   (16bits)
- count  (16bits)

Set the address source and destination of copy. Coutn contains number of byte you want copy. 

- Source is store in address $02 & $03 (low/high)
- Dest is store in address $fb & fb (low/high)
- Count is store in address $fd,$fe (low/high)

*Example:*


### memCopyMod


