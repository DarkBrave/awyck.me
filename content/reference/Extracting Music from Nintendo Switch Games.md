---
title: "Extracting Assets from Nintendo Switch Games"
date: 2026-06-23
---

I wanted to extract the music from some Nintendo Switch games that hadn't been ripped yet. I'm not a lawyer, but whole process should be completely legal, assuming you are using your own dumps, which you should be using regardless.

## Steps

You will need your dumped ROM in either `.xci` or .`nsp` format, alongside your `title.keys` and `prod.keys` extracted from your console, as the dumps can be encrypted. I'm not going to say how to do these things, as I'm a bit worried by the Nintendo Ninjas.

1. Download/clone the [NSTool repository](https://github.com/jakcron/nstool) to a folder on your computer, and put your `prod.keys` in that folder.
- There are other tools for doing this, I just like NSTools since it just kinda works in my experience.

2. Place your `title.keys` file into the `~/.switch/title.keys` location on your computer.
 
3. Extract the base game .NSP/.XCI file using the following command, adjusted to your exact filenames:
```
./nstool -k ./prod.keys -x extracted/ ./switchromdump.nsp
```
- This will extract many `.NCA` files (where the content is), `.CNMT.NCA` files (metadata) and `.TIK`/`.CERT` files (certificates/tickets). Generally your largest .NCA file will have the bulk of the music.

4. If you want to dump an update, you may need to further extract the ticket files by running:
```
./nstool -k ./prod.keys ./extracted/[random stuff].tik
```
and then add them all to new lines in the `title.keys` in step 2's location formated like this:
```
[RightsID from the previous command] = [Data from the previous command]
```

5. Now you can extract the largest `.NCA` files from the dumps:
```
./nstool -k ./prod.keys -x extracted-assets/ ./extracted/[random stuff].nca
```
All music will generally be stored in the `extracted-assets/Sound/Resources/Stream/` folders.

6. You'll need to download/install vgmstream from [their website](https://vgmstream.org/) to actually extract the music to a usable format.

7. Convert the music using the following command while inside a folder with the music:
```bash
for f in *.bwav; do vgmstream-cli -o "out/${f%.bwav}.wav" "$f"; done
```
Replace `.bwav` with whatever format it comes in.

8. If needed, manually assign metadata using a tool like File Explorer or iTunes (I'm sure there's a FOSS alternative), and potentially mix split-up tracks in Audacity.