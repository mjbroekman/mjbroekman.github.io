# Encoding, Obfuscation, and Encryption

## What is encoding?

Encoding a term used in two different contexts.

### Character Encoding
First, there is character encoding. This is how numbers and letters are represented within the computer. The most common form of character encoding is ASCII, in which characters are stored as a single byte of data. Most of the time, your English webpages are written in ASCII, whether they are HTML, ASP, PHP, or JavaScript. Non-English webpages will also use ASCII characters if they share any characters with English, but the rest of the characters are what is called Unicode, an encoding that stores characters in 1 - 4 bytes of data, depending on where the character is in the list of Unicode characters.

* [https://kunststube.net/encoding/](https://kunststube.net/encoding/) has an incredible amount of detail on character encoding and is a good read if you're interested in the minute details.

Regardless of whether a character is ASCII or Unicode, however, it can be further represented as a binary, decimal, octal, or hexadecimal number in most programming languages.

For example: the capital letter A has the ASCII decimal value of 65 (it's the 65th character in ASCII). This means that all of the following examples _are_ the capital letter A:
* '65' => This converts to the capital letter A directly using most languages `chr()` function.
* '\x41' => This is the hexadecimal (\x followed by 2 hexadecimal characters) representation of 65. In some languages, you need to convert it to a decimal and then to a character. In some languages (PHP, for example), a string with hexadecimal values can be directly interpreted without first converting it.
* '\0101' => This is the octal (\ followed by 2 to 4 numbers between 0 and 7) representation of 65. Some languages require that octal numbers start with a `\0`, but not all do.
* '01000001' => This is the binary representation of 65. It must be converted to one of the above representations before it will show up as a capital A. This is rarely used and, outside of a specific case, I haven't seen it used in malware.

Similarly, a return (from hitting the return or enter key on your keyboard) doesn't _look_ like anything on the screen, but it is still a character (or two, if you're on a Windows system) that needs to be represented to the computer. The carriage return and line feed characters are control characters.
* carriage return (the character that tells the cursor to go back to the beginning of the line) is the ASCII character for '13' (or '\x0d' in hex and '\015' in octal)
* line feed (the character that tells the cursor to go down a line) is the ASCII character for '10' (or '\x0a' in hex and '\012' in octal)

### Text Encoding
Now that we have the individual character encoding covered, it's time to talk about text encodings. These differ from character encoding in that they are used to turn text with unpredictable breaks into a single, unbroken string.

Many programs, web browsers included, really do not like taking input with lots of spaces or control characters in them. In the early days of computing, control characters were literally that... characters used to _control_ the behavior of a program or computer. So, if you needed to pass data safely through a program, but needed a way to encode it in such a way that a program wouldn't be impacted by reading the control characters or think that the input ended because of a space or carriage return.

Over the years, there have been several different text encoding formats, but the ones that are most familiar are:
* base64
* urlencode

#### Base64 encoding
This is probably the most common encoding mechanism I see in web pages these days. It takes the vast array of different characters and converts them into the set of letters a through z (both capital and lowercase), the numbers 0 through 9, the `+` symbol, and the `/` symbol. Additionally, the `=` is used to make sure the length of the resulting string is divisible by 4.

This is different from character encoding because base64 encoding takes three 1-byte characters at a time, but writes them out as four characters in order to reduce the character set to printable characters regardless of the original characters used. As a result, while any given string of base64 may decode to a number of different original texts _out of context_, within the context of an entire base64 text, a group of characters will *always* decode to the same original text.

Examples:
* `aHR0cDovL3d3dy5nb29nbGUuY29tCiAgIGh0dHBzOi8vd3d3Lmdvb2dsZS5jb20KICAgaHR0cDovL2dvb2dsZS5jb20KICAgaHR0cHM6Ly9nb29nbGUuY29t`
* `aHR0cDovL3d3dy5nb29nbGUuY29tCmh0dHBzOi8vd3d3Lmdvb2dsZS5jb20KaHR0cDovL2dvb2dsZS5jb20KaHR0cHM6Ly9nb29nbGUuY29t`
* `aHR0cDovL3d3dy5nb29nbGUuY29tCgpodHRwczovL3d3dy5nb29nbGUuY29tCgpodHRwOi8vZ29vZ2xlLmNvbQoKaHR0cHM6Ly9nb29nbGUuY29t`

The above examples have some similar characteristics _within_ themselves that, over time, will be second nature when looking at other strings. For example, we see `aHR0` showing up several times in each string. Based on experience, I _know_ that `aHR0` converts to `htt` and, most specifically, `aHR0cDov` converts to `http:/`. 
