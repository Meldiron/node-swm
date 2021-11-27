# Slime Format

🚨 This file is WIP (work in progress) 🚨

Original `SLIME_FORMAT` was too technical to understand. This document is supposed to provide same information in a structure that is easier to follow and understand, thanks to sectioning, longer explanations, examples and screenshots.

> Original document: https://github.com/Paul19988/Advanced-Slime-World-Manager/blob/develop/SLIME_FORMAT

## Introduction

If you are not experienced with HEX format, I would highly recommend using [Hexed.it](https://hexed.it/) editor. In this editor you can see bunch of bytes. When you first come, it creates grid where every cell is `00`. Every cell holds hexadecinal value that can be anything from 0 (`00`) up to 255 (`FF`). If you want to better understand how to convert decinal number (i.e. 85) to hexadecimal (55), you can use tools such as [rapidtables hex-to-decimal](https://www.rapidtables.com/convert/number/hex-to-decimal.html).

From now on, if I say `1 byte`, it means, the value uses 1 cell. `2 bytes` would mean 2 cels are used and so on... You can see cells are split into grid of rows and columns, but you don't need to worry about it. You fill cells just like you wold read them - you fill first row from left to right, then jump to next row.

Quick example:
```
2 bytes holding B1 0B, required on start of each file
1 byte holding slime format version, decimal number 9 (09 in HEX)
1 byte holding world version, decimal number 7 (07 in HEX)
```

From this description, your HEX file should look like `B1 0B 09 07`:

![CleanShot 2021-11-27 at 11 01 37@2x](https://user-images.githubusercontent.com/19310830/143676755-12adc5a2-c7e8-42e4-afa9-ed21b44c3ee4.png)

Now you should understand how to convert decimal numbers to hexadecimal and how to use website Hexed to fill these HEX values into a file.

## Format sections

Slime format is split into multiple sections and each represents different part of Minecraft world.

### "Magic"

First 2 bytes of file has to be `B1 0B`. No idea why. No description. Let's imagine it's some kind of header that needs to be at the beginning of every slime file.

### Slime format version

1 byte holding version of slime format that we are using in our file. Available versions and their changelogs are:

```
Slime format version history:
 - v1: Initial release.
 - v2: Added "extra" nbt tag for per-world custom data.
 - v3: Added entities storage.
 - v4: Added support for 1.13 worlds and removed HypixelBlocks3.
 - v5: Skylight and blocklight might not always be present.
 - v6: Added world versioning
 - v7: Added world maps
 - v8: Variable biomes size
 - v9: Fix issue with biomes size, causing old worlds to be corrupted
```

For example, I want to use latest version, v9, so I use `09`.

## Examples

TODO: Show example of world with 3x3 cube in a world
