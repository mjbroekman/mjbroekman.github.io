# Encryption
{:.no_toc}

## Basics
Encryption goes one step further than obfuscation or encoding, making the code impossible to execute without knowing the 'secret' used to make the encrypted text. Whether the secret is a password or another file or some system characteristic, these files are the hardest to analyze. _For the most part_, these are beyond the scope of what we will be looking at because cracking the encryption is non-trivial.

## Sample
One of the more common password encrypted files that I've seen contains a `decode` function that looks like this:
~~~~~~
    function decode($String){
          $String = base64_decode($String);
          $Salt=$_COOKIE["dolly_password"];
          $StrLen = strlen($String);
          $Seq = "adfasddsSAAfgdca";
          $Gamma = "";
          while (strlen($Gamma)<$StrLen)
            {
              $Seq = pack("H*",sha1($Gamma.$Seq.$Salt));
              $Gamma.=substr($Seq,0,8);
            }

          return $String^$Gamma;
    }
~~~~~~

This function takes a string that has been encoded using the base64 encoding mechanism as an argument.
It then runs through a series of steps to get the decrypted text:
1. It takes a password from a cookie that has been set by the client
2. Determines the length of the _decoded_ string
3. Sets a predetermined add-on string
4. And initializes another variable (Gamma).

After doing that, it loops through the following operations until Gamma is no longer shorter than the decoded string:
1. Takes the value of Gamma and concatenates the predetermined string and the password to the end.
2. Generates the [SHA1](https://en.wikipedia.org/wiki/SHA-1) hash of that resulting string, resulting in a 40 character string.
3. Converts the 40 character hash into a 20 character binary string (In PHP, the pack function, as used in this example, expects the input to be a hexadecimal string which it then turns into a binary string using every two characters as the value of the packed character).
4. Appends the first 8 characters from that string to the end of Gamma.

Once the decode function has built this Gamma string, it performs a bitwise operation known as XOR [^1] with the decoded original string and returns that value.

The code then continues from there. If the cookie is set to the incorrect value, the resulting string won't be valid for the next operation in the code and you will get an error.

[^1]: {% include XOR.html %}
