# Steganography
{:.no_toc}

* TOC
{:toc}

## What is Steganography
In computing terms, steganography is the practice of hiding a computer file, message, image, or video within another file, message, image, or video[^1]. The container file is, for all intents and purposes, a valid file but, when executed (in the case of images) or parsed slightly differently, the hidden payload is executed instead.

## Variations
### Image Embedded Code
By far the most common form of steganography found on sharing hosting providers, these files appear to be images and can be loaded as if they were images. Depending on the format, they may appear broken but that is not guaranteed.

Often, the `GIF89a` header is placed at the top of the file, causing many programs to think that it is an image file. Occasionally, I've seen code embedded in the EXIF data of JPEG files or behind a PNG header.

In these cases, opening the file in an editor will reveal the code easily, but from the browser side it is invisible.

### Whitespace Code
The most unique steganographic technique that I've encountered is whitespace-encoding[^2] [^3]. I came across this in 2014 and it took me a while to fully understand what was going on. In short, the payload (the version I came across was in PHP, but there is no reason that it can't be performed in other languages) was encoded as a string of spaces and tabs that were then turned into 1s and 0s and then, in groups of 8 digits, converted to their ASCII character and executed.

The samples that have been examined in the linked articles are, however, very obviously out of place when the file is examined. The _implications_ of this technique, however, are frightening. All that is needed is a script that ignores **non-whitespace** in a normal file and converts the remaining whitespace to the appropriate executable code.


#### Footnotes
[^1]: Wikipedia - [Steganography](https://en.wikipedia.org/wiki/Steganography)
[^2]: Sucuri - [https://blog.sucuri.net/2011/05/oscommerce-malware-cannot-redeclare-corelibrarieshandler.html](https://blog.sucuri.net/2011/05/oscommerce-malware-cannot-redeclare-corelibrarieshandler.html)
[^3]: VirusBulletin - [https://www.virusbulletin.com/blog/2011/06/malicious-php-script-hides-bad-stuff-inside-white-space/](https://www.virusbulletin.com/blog/2011/06/malicious-php-script-hides-bad-stuff-inside-white-space/)
