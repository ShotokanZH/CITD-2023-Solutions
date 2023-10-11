# Corrupted
By analyzing the image headers we notice the following:
```
00000000  ff d8 ff db 0d 0a 1a 0a  00 00 00 0d 49 48 44 52  |............IHDR|
```

`IHDR` is a chunk specifically found in PNG images, but the image we are analyzing has a `.jpg` extension and magic bytes.

By replacing the magic bytes with the PNG ones (`89 50 4E 47 0D 0A 1A 0A`) and renaming it `.png` the image magically appears.
