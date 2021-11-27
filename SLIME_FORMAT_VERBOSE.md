# Slime Format

🚨 This file is WIP (work in progress) 🚨

- TODO: Use grammarly at the end
- TODO: Don't forget about examples
- TODO: Make sure to explain every section

Original `SLIME_FORMAT` was too technical to understand. This document is supposed to provide same information in a structure that is easier to follow and understand, thanks to sectioning, longer explanations, examples and screenshots.

> Original document: https://github.com/Paul19988/Advanced-Slime-World-Manager/blob/develop/SLIME_FORMAT

## Introduction

If you are not experienced with HEX format, I would highly recommend using [Hexed.it](https://hexed.it/) editor. In this editor you can see bunch of bytes. When you first come, it creates grid where every cell is `00`. Every cell holds hexadecinal value that can be anything from 0 (`00`) up to 255 (`FF`). If you want to better understand how to convert decinal number (i.e. 85) to hexadecimal (55), you can use tools such as [rapidtables hex-to-decimal](https://www.rapidtables.com/convert/number/hex-to-decimal.html).

From now on, if I say `1 byte`, it means, the value uses 1 cell. `2 bytes` would mean 2 cels are used and so on... It's important to note that if there are multiple bytes representing same information, their vaues are not split. With 1 byte the maximum value is 255 (FF), but with 2 bytes maximum is 65535 (FFFF). This is really handy when storing bigger numbers, such as world's width.

You can see cells are split into grid of rows and columns, but you don't need to worry about it. You fill cells just like you wold read them - you fill first row from left to right, then jump to next row.

Quick example:
```
2 bytes holding B1 0B, required on start of each file
1 byte holding slime format version, decimal number 9 (09 in HEX)
1 byte holding world version, decimal number 7 (07 in HEX)
```

From this description, your HEX file should look like `B1 0B 09 07`:

![CleanShot 2021-11-27 at 11 01 37@2x](https://user-images.githubusercontent.com/19310830/143676755-12adc5a2-c7e8-42e4-afa9-ed21b44c3ee4.png)

Now you should understand how to convert decimal numbers to hexadecimal and how to use website Hexed to fill these HEX values into a file. This knowledge is required before continuing to next section, please make sure you fully understand it.

---

If you are not experienced with Minecraft worlds, you need to understand few basic concepts:

**3D space**
Minecraft is a 3D world, so it has 3 axis - X, Y and Z.
X and Z represents 2D space, just like floor in your house.
Y represents how high something is. Your carpet is right above the floor (Y=1) but your window starts few centimeters above floor (Y=10).

**Blocks, chunks and regions**
Everything in Minecraft is a block. If you imagine something bigger, like a door in your house, that would consists of multiple blocks. Each block has a location explaining where it is with 3 numbers: X, Y and Z.

Every X=16 Z=16 space in a Minecraft world creates a chunk. Chunk does not have Y, because it holds every block from lowest (Y=0) to highest (Y=255) point.

Just like chunks groups blocks, regions groups chunks. Every region represents 32x32 space of chunks. Again, Y does not matter.

---

Quick example, let's say we have block on location X=5125 Y=60 Z=-2250.

First, let's figure out in which chunk this block is. If we do `5125/16` (because each chunk holds 16 blocks), the answer is `320,3125`, so the block is somewhere inside in chunk X=320. If we do same for Z, `-2250/16=-140,625`, block has to be in chunk on Z=-140. We put these two together and we know our block is in chunk X=320 Z=-140.

We can do the same for regions too! Each region holds 32 chunks, so to get X, we do `320/32=10`, which means, region's X has to be 10. Same for Z, `-140/32=-4,375`, so regionps Y is -4. We put that together and we know te chunk (so block too) is in region X=10 Y=-4.

## Format sections

Slime format is split into multiple sections and each represents different part of Minecraft world.

### "Magic"

First 2 bytes of file has to be `B1 0B`. No idea why. No description. Let's imagine it's some kind of header that needs to be at the beginning of every slime file.

### Slime format version

1 byte holding version of slime format that we are using in our file. Available versions are:

```
Slime format version history:
 - Version 1: 01
 - Version 2: 02
 - Version 3: 03
 - Version 4: 04
 - Version 5: 05
 - Version 6: 06
 - Version 7: 07
 - Version 8: 08
 - Version 9: 09
```

> To learn more about changes in each version, you can take look at [Original SLIME_FORMAT](https://github.com/Paul19988/Advanced-Slime-World-Manager/blob/develop/SLIME_FORMAT#L90-L99).

For example, I want to use latest version, v9, so I use `09`.

### Minecraft world version

1 byte holding MC world version. Depending on what version is your Minecraft server is running on, you would need the same version described here, in the world file. Available versions are:

```
 - MC 1.8 world: 01
 - MC 1.9 world: 02
 - MC 1.11 world: 03
 - MC 1.13 world: 04
 - MC 1.14 world: 05
 - MC 1.16 world: 06
 - MC 1.17 world: 07
```

My server is running on version 1.17, so my byte will hold `07`.

### TODO: 2 bytes (short) - xPos of chunk lowest x & lowest z

### TODO: 2 bytes (short) - zPos

### World width (in chunks)

2 bytes explaining how many chunks there are on X axis. Let's say my building starts on X=-234 and ends on X=2422, then my building is inside chunks from X=-14 to X=151. That means, there are 165 chunks on total on X axis, so the value of these 2 bytes needs to be `00 A5` (165 in HEX).

> Notice how I filled my value only in the second byte. If you have more bytes than you need, you can fill remaining with `00`. Empty bytes should always be the first ones. For example, if I had 1250 chunks on X axis, I would fill in `04 e2`, using both bytes.

### World depth (in chunks)

2 bytes; World depth works similiar to it's width, but instead of X location, we care about Z location. Let's say my building start on Z=50, ends on Z=80, I use chunks from Z=3 to Z=5, so my world depth is 2. I would set these 2 bytes to `00 02`.

## Examples

TODO: Show example of world with 3x3 cube in a world
