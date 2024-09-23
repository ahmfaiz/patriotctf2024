# Patriot Ctf 2024 WriteUp

## OSINT

### Porcelain Throne

```
### 100

Beginner

This toilet gives you the best view in the at sunset, which city is this located in? Flag format will be PCTF{cityname}
```

File : throne.JPG

#### Solution attempt:

1. Ran exiftool. Found nothing interesting.
2. Ran reverse image search through Google images. Found similar looking setting in Daegu’s E-World 83 Tower post on reddit.
3. City = Daegu

**Flag = PCTF{daegu}**
Solved 😁.

### On The Run

```
### 100

Easy

We've been tracking the adversary for weeks, and he just slipped up and posted this gorgeous high-rise view on his Twitter. His caption was "awesome meeting with a gorgeous view!" Can you track down his location?

**Flag format will be PCTF{<business name of his location>}. Not a street address. If he were in a WeWork space, it would be PCTF{wework}.**

Author: Shiloh Smiles (arcticx)
```

File: twitterpost.JPG

#### Solution attempt:

1. Ran exiftool. Found nothing interesting.
2. Ran reverse image search. Found similar views, but none mentioned the space name. Found one [LinkedIn post](https://www.linkedin.com/posts/leon-l-brittain-b25687_2023-was-quite-a-year-one-of-my-most-memorable-activity-7148485118175715328-TnhS/) thanking the space providers for the event: Convene

**Flag = PCTF{convene}**
Solved 😀.

### Phase One

```
### 100

Easy

We had one of our agents infiltrate an adversary's lab and photograph a gateway device that can get us access to their network. We need to develop an exploit as soon as possible. Attached is a picture of the device. Get us intel on what MCU the device is utilizing so we can continue with our research.

**Flag format: pctf{mcu_vendor_name} (example: pctf{broadcom}**

Author: Dylan (elbee3779)
```

File: target_product.jpg

#### Solution attempt:

1. Searched full model name on Google. Found no good info.
2. Searched for mcu on Brave search (good for more technical stuff due to not favouring google SEO optimizations).
3. Opened top 5 results. Found answer on: [Deviwiki](https://deviwiki.com/wiki/D-Link_DSL-6300V)

**Flag: pctf{ikanos}**
Solved 🎉.


### Give me four words, Vasily

```
### 290

Easy

We have been tracking a highly suspicious submarine believed to be harboring many enemy skiddies. Unfortunately, this satellite image is rather out of date. Your mission is to locate the submarines there using a more up-to-date image, and tell us what class they are with their NATO reporting name - a letter from the NATO phonetic alphabet, spelled out.

We want to know precisely where the aft end of northernmost submarine attached to the pier is. Communicate its location in three words. Include the NATO reporting name of the class of submarine in your answer.

Submission format: PCTF{three.position.words.class_name} Example submission: PCTF{employing.broken.imports.sierra}

Author: James Crowley (@zephyrone3956)
```

File: sbububmarine.png

#### Solution attempt:

1. Ran reverse image search. Found same image on Twitter through Google images which had coordinates 😁.
2. Opened coordinates in [what3words](https://what3words.com/bagels.light.vivid) and found square with aft end of desired submarine. Copied 3 words: *bagels.light.vivid*.
3. Searched for port name and found [Wikipedia article](https://en.wikipedia.org/wiki/Polyarny,_Murmansk_Oblast). Article had image of same submarine with class name: *kilo*.
4. Combine both and you've got your flag.

**Flag: PCTF{bagels.light.vivid.kilo}**
Solved 😁.

### Dirty Laundry

```
### 466

Hard

We have been tracking the leader of an international crime ring, who we believe is laundering money through his girlfriend's business. We believe he is fleeing the country under the guise of a vacation with her. All we have on her is the name "Adrianna" and some business receipts from a "Patriot Corporation LLC". Can you help us figure out where these two have gone off to? (note: there will be a real flag for this with "PCTF" and all.)

Author: Shiloh Smiles {arcticx}
```

#### Solution attempt:

1. Search for Adrianna and Patriot Corporation LLC.
2. Found two LinkedIn profiles: https://www.linkedin.com/in/adrianna-hunnel-2b4267307/ and https://www.linkedin.com/in/demetrius-trenderason-37a760329/. 
3. Unable to find anything new. :(

## Misc

### Green Eggs and Ham

```
### 500

Medium

A disgruntled timekeeper here at Bell Labs recently exfiltrated some data from our network. The crappy network down there might have caused enough errors to make it useless... right?

Author: Shiloh Smiles (arcticx)
```

File: exfil.pcap

#### Solution attempt:
1. Opened file in Wireshark.
2. Followed TCP streams and found interesting file upload in stream 13.
3. Converted hex dump to file and got a zip file. Zip file was password protected. Kept for later. :(
4. Looked at request being made to arbitrary directories which all seemed to be hex values. Gathered all hex values in one place.
5. Hint by the organizers suggested hamming codes.
6. Tried decoding hamming codes for many combination (7,4), (8,4), etc. but was unable to find anything interesting.

### Really Only Echo

```
### 200

Easy

Hey, I have made a terminal that only uses echo, can you find the flag?

Author: Ryan Wong (shadowbringer007)

`nc chal.competitivecyber.club 3333`
```

File: server.py

#### Solution attempt:

1. I edited the code to be able to test it in my editor. You can play with this code as well:
```python
#!/usr/bin/python3
import os
blacklist = os.popen("ls /bin").read().split("\n")
blacklist.remove("echo")
#print(blacklist)

def filter_check(command):
    user_input = command
    parsed = command.split()
    print("First split: V")
    print(parsed)
    #Must begin with echo
    if not "echo" in parsed:
        return False
    else:
        if ">" in parsed:
            print("HEY! No moving things around.")
            return False
        else:
            parsed = command.replace("$", " ").replace("(", " ").replace(")", " ").replace("|"," ").replace("&", " ").replace(";"," ").replace("<"," ").replace(">"," ").replace("`"," ").split()
            print("After replacing punc: V")
            print(parsed)
            for i in range(len(parsed)):
                if parsed[i] in blacklist:
                    return False
            return True

def backend():
    print('This is shell made to use only the echo command.\n')
    while True:
        print('Please input command: ')
        user_input = input()
        #Check input
        if user_input:
            if filter_check(user_input):
                output = os.popen(user_input).read()
                print("OUTPUT: " + output)
            else:
                print("HEY! I said only echo works.\n\n")
        else:
            print("Why no command?")

def main():
    backend()

if __name__ == '__main__':
    main()
```

2. I knew I had to run the `ls` command somehow first.
3. We can see that the command must have echo in it. So the simplest way to achieve desired output would be `echo $(ls)`.
4. But after replacing special characters `ls` command is blacklisted.
5. I noticed that slashes (`/`) are not being sanitized.
6. So I used `echo $(/bin/ls)` as split command splits at spaces, so the command would be `/bin/ls` instead of `ls`. And it worked. I could see a file named `flag.txt`.
7. So I ran `echo $(/bin/cat flag.txt)` and voilà!

**Flag: pctf{echo_is_such_a_versatile_command}**
Solved 😁.

### Let's Play [steg]Hide & Seek

```
### 420

Medium

Not much of a backstory here... there is an embedded flag in here somewhere, your job is to find it.

Author: David Morgan (r0m)
```

File: qr_mosaic.bmp

#### Solution attempt:

1. Challenge name suggest steghide tool. Ran `steghide extract --stegofile qr_mosaic.bmp`.
2. Entering blank passphrase gave file: `patriotCTF.bmp`.
3. Opened file in hex editor for analysis.

Details found:
(from the BMP specifications)

**Size of the BMP file** = 0x04AB22 = 305954 bytes
**Offset where the pixel array (bitmap data) can be found** = 0x36
**Number of bits per pixel** = 0x18 = 24 bits = 3 bytes
**Width of the bitmap in pixels** = 0x02fe = 766
**Height of the bitmap in pixels** = 0x85 = 133
The dimensions of the image are inline with the size, hence can't resize the image.

Unable to proceed further.

## Forensics

### Simple Exfiltration

```
### 100

Easy

We've got some reports about information being sent out of our network. Can you figure out what message was sent out.

Author: Ryan Wong (shadowbringer007)
```

File: exfiltration_activity_pctf_challenge.pcapng

#### Solution attempt:

1. Opened file in Wireshark.
2. looked through file and found repeating HTTP requests.
3. Unable to make anything of them :(

