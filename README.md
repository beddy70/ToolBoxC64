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
- Dest is store in address $fb & $fc (low/high)
- Count is store in address $fd,$fe (low/high)

*Example:*
```
lda #$00 ; source $0400
sta $02
lda #$04
sta #03

lda #$e0 ; destination $05E0
sta $fb
lda #$05
sta $fc 

lda #$a0 ; number of bytes $a0
sta $fd
lda #$00
sta $fe

jsr memcopy
```
  
### memCopyMod


