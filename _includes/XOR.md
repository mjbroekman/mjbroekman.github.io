The XOR (exclusive OR) operation compares the binary representation of the arguments and returns a 0 or 1 depending on whether each bit in the string is the same (in which case you get a 0 returned) or different (in which case you get a 1).

Example
~~~~~~
    A ^ A  ==> NUL
~~~~~~
`A` in binary is `01000001`. Since we get a 0 when two digits in the same position are the same, `A ^ A` results in `00000000` which is the binary representation of the NUL character.
~~~~~~
    _ ^ 0 ==> o
~~~~~~
`_` in binary is `01011111`. The number `0` converted into binary is actually `00110000`. When we XOR these together we get:
~~~~~~
   01011111
   00110000
   =======
   01101111
~~~~~~
This is the binary representation of the lowercase letter `o`.
