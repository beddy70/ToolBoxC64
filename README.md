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

- Source is store in address $02 and $03 (low/high)
- Dest is store in address $fb and $fc (low/high)
- Count is store in address $fd and $fe (low/high)

*Example:*
```
*=$1000
start:
	lda #$00 ; source $0400 top of default screen of C64
	sta $02
	lda #$04
	sta $03

	lda #$e0 ; destination $05E0 ( 13 lines under ) 
	sta $fb
	lda #$05
	sta $fc 

	lda #$a0 ; number of bytes $a0 ( 4*40 -> 4 lines)
	sta $fd
	lda #$00
	sta $fe

	jsr memCopy

	rts

!source "libs/memtools.a"
```
  Before | After
------------ | -------------
![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/memtool1.png) | ![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/memtool2.png)

  
  
### memCopyMod


