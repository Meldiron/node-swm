# Slime Format

🚨 This file is WIP (work in progress) 🚨

- TODO: Use grammarly at the end
- TODO: Don't forget about examples
- TODO: Make sure to explain every section

Original `SLIME_FORMAT` was too technical to understand. This document is supposed to provide same information in a structure that is easier to follow and understand, thanks to sectioning, longer explanations, examples and screenshots.

> Original document: https://github.com/Paul19988/Advanced-Slime-World-Manager/blob/develop/SLIME_FORMAT

## Introduction

If you are not experienced with HEX format, I would highly recommend using [Hexed.it](https://hexed.it/) editor. In this editor you can see bunch of bytes. When you first come, it creates grid where every cell is `00`. Every cell holds hexadecinal value that can be anything from 0 (`00`) up to 255 (`FF`). If you want to better understand how to convert decinal number (i.e. 85) to hexadecimal (55), you can use tools such as [rapidtables hex-to-decimal](https://www.rapidtables.com/convert/number/hex-to-decimal.html).

From now on, if I say `1 byte`, it means, the value uses 1 cell. `2 bytes` would mean 2 cels are used and so on... It's important to note that if there are multiple bytes representing same information, their vaues are not split. With 1 byte the maximum value is 255 (FF), but with 2 bytes maximum is 65535 (FFFF). This is really handy when storing bigger numbers, such as world's width. Also, if you have more bytes than you need, you fill it with `0` from the beginning. Let's say I had 2 bytes, so 4 hex symbols. Let's say I wanted to store number 165 - that is `A5` in HEX. But I have 2 bytes, right? I would ned to store it as `00 A5`, so I fill both bytes. Another example, let's say I want to store 1250 - that's `4e2` in HEX. But I need 2 bytes, right? That is 4 HEX symbols. I add zero to create `04 E2`.

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
 - SLIME_FORMAT Version 1: 01
 - SLIME_FORMAT Version 2: 02
 - SLIME_FORMAT Version 3: 03
 - SLIME_FORMAT Version 4: 04
 - SLIME_FORMAT Version 5: 05
 - SLIME_FORMAT Version 6: 06
 - SLIME_FORMAT Version 7: 07
 - SLIME_FORMAT Version 8: 08
 - SLIME_FORMAT Version 9: 09
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

I didn't understand this part properly yet, but I noticed most works has really high numbers here, so for now I would say to always put `FF FF` here - giving you no limitations.

### TODO: 2 bytes (short) - zPos

Same as above. Always use `FF FF`

### World width (in chunks)

2 bytes explaining how many chunks there are on X axis. Let's say my building starts on X=-234 and ends on X=2422, then my building is inside chunks from X=-14 to X=151. That means, there are 165 chunks on total on X axis, so the value of these 2 bytes needs to be `00 A5` (165 in HEX).

### World depth (in chunks)

2 bytes; World depth works similiar to it's width, but instead of X location, we care about Z location. Let's say my building start on Z=50, ends on Z=80, I use chunks from Z=3 to Z=5, so my world depth is 2. I would set these 2 bytes to `00 02`.

### Chunk bitmask

This section will explain pretty complex concept, please try to carefully follow it and understand as much as possible. Under this section, there is full example on actual world to showcase everything.
  
_This section can take as many bytes as you need. Amount of bytes used will depend on size of your world, as explained later in this section._

Each byte consists of 8 bites. Each chunk in this bitmap represents one bit. If the chunk contains **anything** (even a single block), then it's represented by bit `1`. If the chunk is full of air, it's represented by bit `0`.

Size of our bitmap in bytes is tightly coupled with world's width and depth. The formula is `ceil((width*depth) / 8) bytes`.
That means, if our width is 9 and height 12 (meaning we have 9x12 chunks), then the formula would say `ceil((9*12) / 8)= 14 bytes`, meaning our bitmap section is supposed to be 14 bytes long. You don't really need to worry about this, it's just a quick test you can do to double-check if you calculated bitmat correctly.

The order of chunks is descending Z, then descending X. In MySQL world that would be `ORDER BY z DESC, x DESC`. You can find out more about [multipel column ordering](https://stackoverflow.com/questions/2051162/sql-multiple-column-ordering). The question is about MySQL, but same ordering logic applies here.

If you end-up with unused bits on last byte, you add zeros on the left to finish byte. For example, if my bitmap was `010 01011110 11010101`, then I would need to add `00000` at the beginning to finish the byte, resulting in `00000010 01011110 11010101`.

> If you visually group bits in group of eights, you can easily see the pattern. You can simply make sure that every section has 8 numbers, including last one.

---

**#example-break**

_Feel free to skip this section if you understand every format section explained so far. If not, this section should help you understand missing parts._

In this section we will take simple Minecraft world and try to create slime format of it (only using what we learned so far). The world we are going to be creating is this:

![image](https://user-images.githubusercontent.com/19310830/143684648-01f9cf9a-5047-400e-98e2-62cbf4861c94.png)

First, we need to understand boundaries of our building. We can ignore Y, but we will need to imagine rectangle of our building and find smallest and biggest X and Z of this imaginary selection.

![image](https://user-images.githubusercontent.com/19310830/143684655-35f6816f-9297-427a-9239-68e0812835fa.png)

As described on the screenshot, my smallest point is `X=-1 Z=-5` and my highest point is `X=18 Z=2`. From these values, I can easily calculate smallest and highest X and Z by finding in which chunk these 2 blocks are. My smallest block is in chunk `X=-1 Z=-1` and biggest one is in chunk `X=1 Z=0`.

With that in mind, by doing simple math I find out my building uses space of 3x2 chunks.

Now we should have all required information ready, let's start building slime world!

We start with `B1 0B`, as every slime world does. Now we need slime version. I use v9, so `09`. I continue by adding MC version - since I am using 1.13, the version hex is `07`. For lowest allowed chunk size I use `FF FF` for X and `FF FF` for Z.

We already know world's used space in chunk, it's 3x2 from earlier. I first fill X by adding `00 03`, then Y by adding `00 02`.

Lastly, I need a chunk bitmask. From formula showed earlier, I find out my chunks will only use 1 byte. By taking a bird-view of all chunks, I find out all chunks includes at least one block, except chunk `X=1 Z=0` and `X=-1 Z=-1`. To build my bitmap, I create a table with my chunks in order and mark if they includes at least one block or not:

| Chunk X | Chunk Z | Includes block(s) |
|---------|---------|-------------------|
| 1       | 0       | No (0)            |
| 0       | 0       | Yes (1)           |
| -1      | 0       | Yes (1)           |
| 1       | -1      | Yes (1)           |
| 0       | -1      | Yes (1)           |
| -1      | -1      | No (0)            |

That means, my binary code is `01 1110`, and to finish byte, I will add remaining zeros: `0001 1110`. After converting that from binary to HEX, I get `1E`.

My final slime world with all sections we learned so far is `B1 0B 09 07 FF FF 00 03 00 02 1E`.

> Slime world with my structure can be found here:
> [wooden_floor.zip](https://github.com/Meldiron/node-swm/files/7611836/wooden_floor.zip)

---

## Examples

If you dont understand any part of slime format, the best way to understand is to see an example of actual world. I prepared multiple examples in different scenarios for you to better understand the implemetation

TODO: Write examples
