# ToolBoxC64

Here, you can find some tools in assembler 6502 for Commodore 64.

## memtools.a 

This library, contains some routine to make copy in memory. 

### memCopy

**memCopy** *source,dest,count*

- source (16bits) 
- dest   (16bits)
- count  (16bits)

Set the address source and destination of copy. Count contains number of byte you want copy. 

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
![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/images/memtool1.png) | ![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/images/memtool2.png)

  
  
### memCopyMod

This routine is like memCopy but with modulo. See below :  

**Source data:**
```
      [------ line size (number of bytes -------]   ; Here 11 bytes
.byte $80,$80,$80,$80,$80,$80,$80,$80,$80,$80,$80   ; line 0
.byte $80,$80,$80,$80,$80,$80,$80,$80,$80,$80,$80   ; line 1
.byte $80,$80,$80,$80,$80,$80,$80,$80,$80,$80,$80   ; line 2
.byte $80,$80,$80,$80,$80,$80,$80,$80,$80,$80,$80   ; line 3

; here count = 4*11
```
**Result on screen:**
```
-------------------------------
|                             |
|      ------ <-----------modu| 
|lo--->|data|                 |
|      |    |                 |
|      ------                 |
|                             |
|                             |
-------------------------------

Here modulo is number of bytes between the end of first line and the beginning of the second line 
```
**memCopyMod** *source,dest,count*

- source   (16bits) 
- dest     (16bits)
- count    (16bits)
- linesize (16bits)
- modulo   (16bits)

initialize memCopyMod

- Source is store in address $02 and $03 (low/high)
- Dest is store in address $fb and $fc (low/high)
- Count is store in address $fd and $fe (low/high)
- line size is store in address $62 and $63
- modulo is store in address $66 and $67

*Example:*

```
*=$1000

COL = 20
ROW = 6

start:
	lda #<mydata 
	sta $02
	lda #>mydata
	sta $03

	lda #<($0400+(40*ROW+COL)) ; destination on column 20 and row 6
	sta $fb
	lda #>($0400+(40*ROW+COL))
	sta $fc 

	lda #<(enddata-mydata)  ; number of bytes 
	sta $fd
	lda #>(enddata-mydata)
	sta $fe

	lda #<(sl-mydata)		; linesize
	sta $62
	lda #>(sl-mydata)
	sta $63

	lda #<(40-(sl-mydata))		; modulo
	sta $66
	lda #>(40-(sl-mydata))
	sta $67

	jsr memCopyMod

	rts

!source "libs/memtools.a"	

mydata: 

	!text "  *****  "
sl:	!text " *     * " 
	!text "*  ^ ^  *"
	!text " * 0 0 * "
   	!text " *  o  * "
    	!text " *     * "
    	!text "  *****  "
    	!text "   * *   "
    	!text "   * *   "
     
enddata:
```

  Before | After
------------ | -------------
![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/images/memtool1.png) | ![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/images/memtool3.png)

  
### memFill *value,dest,count*

- value (8bits) 
- dest   (16bits)
- count  (16bits)

This routine fill memory of value n. 

- Value is store in address $02 
- Dest is store in address $fb and $fc (low/high)
- Count is store in address $fd and $fe (low/high)

*Example:*

```
!to "PRG/mem.prg", cbm

*=$1000

COL = 0
ROW = 10

start:
	

	lda #32+128 ;INVERT SPACE CHAR 
	sta $02

	lda #<($0400+(40*(ROW))+COL) ; destination $05E0
	sta $fb
	lda #>($0400+(40*(ROW))+COL)
	sta $fc 

	lda #<(40*4)  ; number of bytes $a0
	sta $fd
	lda #>(40*4)
	sta $fe

	jsr memFill

	rts

!source "libs/memtools.a"

```

  Before | After
------------ | -------------
![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/images/memtool1.png) | ![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/images/memtool4.png)


### memMapper

This routine read data from the memory source and write in destination value from a map table.
The map table is array with size of 256 bytes 

Imagine source data : 

$00,$01,$04,$04,$04,$01,$02,...

Map table is : 

$F0,$E0,$D0,$C0,$B0,...

Result is destination is : 

$F0,$E0,$B0,$B0,$B0,$E0,$F0 

- source   (16bits) 
- dest     (16bits)
- count    (16bits)
- mapTbale (16 bits)

Set the address source and destination of copy. Count contains number of byte you want copy. Maptable contains the values to convert from source. 

- Source is store in address $02 and $03 (low/high)
- Dest is store in address $fb and $fc (low/high)
- Count is store in address $fd and $fe (low/high)
- MapTbale is store in address $62 and $63 (low/high)

*Example:*

```
*=$1000	

COL = 20
ROW = 6

start:

	lda #<mydata 
	sta $02
	lda #>mydata
	sta $03

	lda #<($0400+(40*ROW+COL)) ; destination on column 20 and row 6
	sta $fb
	lda #>($0400+(40*ROW+COL))
	sta $fc 

	lda #<(enddata-mydata)  ; number of bytes 
	sta $fd
	lda #>(enddata-mydata)
	sta $fe

	lda #<(sl-mydata)		; linesize
	sta $62
	lda #>(sl-mydata)
	sta $63

	lda #<(40-(sl-mydata))		; modulo
	sta $66
	lda #>(40-(sl-mydata))
	sta $67

	jsr memCopyMod

	lda #$00 ; source $0400 top of default screen of C64
	sta $02
	lda #$04
	sta $03

	lda #$00 ; destination $D800  (color ram)
	sta $fb
	lda #$D8
	sta $fc 

	lda #$e8 ; number of bytes $3e8 (number of caractère in a screen 40x25) 
	sta $fd
	lda #$03
	sta $fe

	lda #<mapTable
	sta $62
	lda #>mapTable
	sta $63

  	jsr memMapper

	rts

mapTable: 
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F

	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F

	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F

	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F
	!byte $00,$01,$02,$03,$04,$05,$06,$07,$08,$09,$0A,$0B,$0C,$0D,$0F



!source "libs/memtools.a"	

mydata: 

	!text "  *****  "
sl:	!text " *     * " 
	!text "*  ^ ^  *"
	!text " * 0 0 * "
   	!text " *  o  * "
	!text " *     * "
	!text "  *****  "
	!text "   * *   "
	!text "   * *   "
     
enddata:
```
  Before | After
------------ | -------------
![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/images/memtool1.png) | ![Alt Text](https://github.com/beddy70/ToolBoxC64/blob/main/images/memtool5.png)
	









