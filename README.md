# AES

This implementation of the Advanced Encryption Standard (AES) for Encryption and Decryption 
in Java closely follows the [AES Standard](https://nvlpubs.nist.gov/nistpubs/fips/nist.fips.197.pdf).

###### TEAM - Bao Nhi Thai, Lauren Doan, Priya Patel, Vaishali Jayaraman

To compile the program:

	javac AES.java

To run the program, provide the following as arguments, respectively:

- key size (128 or 256), 

- key file,

- input file, 

- output file and 

- the mode ("encrypt" or "decrypt").

		java AES {key_size} {key_file} {input_file} {output_file} {mode}
	
For example,
	
	java AES 128 key input output encrypt


#### Overview

Once the 5 command line arguments are parsed, an array of 16 bytes is used to read the input. 

Then, the parameters involved in the cipher are set:

- nk - the number of words (32-bit) in the key file

- nr - the number of rounds required for AES

The computation for the above is worked out as follows:

Our implementation  supports the 128 and 256 bit keys. For the 128 bit key, there are 4 words 
(128/32) and 10 rounds. For the 256 bit key, there are 8 words (256/32) and 14 rounds. 

Encryption and Decryption is carried out 16 bytes at a time. 

A 4 by 4 matrix is used to encrypt and decrypt the data from the byte array. To create this 
matrix, a helper function, buildState is used. 

#### Encryption

If encrypting, the helper function cipher is called with the state, key, nr(rounds) and nk(words),
which carries out encryption. 

First, the key expansion is performed. 

Then, the round key is used to generate a new state. 

The state is modified in each round of encryption by helper functions: subBytes, shiftRows, 
mixCols and addRoundKey. After all rounds are complete, the state is returned by the cipher 
function.

Then, it is written to the output file.

#### Decryption

If decrypting, the helper function invCipher is called with the state, key, nr(rounds) and 
nk(words).

First, the key expansion is performed. 

Then, the round key is used to generate a new state. 

The state is modified in each round of encryption by helper functions: invSubBytes, 
invShiftRows, invMixCols and addRoundKey. After all rounds are complete, the state is 
returned by the invCipher function.

Then, it is written to the output file.

#### Padding

The padding is a little different for each mode, although we have used ZeroLength Padding. 
For encryption, the bytes added are 0's, with the last entry being the number of padding bytes. 
After padding, the input is encrypted and written to the output.

For decryption, the padding is removed and then written to the output file.

#### Helper Functions

The helper functions are explained in more detail below:

- buildState: It accepts a byte array as input and returns a representation of the data in 
the form of a 4 x 4 matrix, the state.

- subBytes: Accepts a byte array. For each byte, finds the corresponding value in a look-up
table and replaces the repective byte value in the state.

- shiftRows: Accepts the state and shifts each row by its row number.

- mixCols: Each column of the state is multiplied by a corresponding matrix of integers using
specific tables.

- addRoundkey: Accepts the state, word and round number, and the round key is added to the state 
using an XOR operation. 

- keyExpansion: Accepts the cipher key, a word, nk and nr and returns a word. In entirety, the 
function returns (4xNr + 4) words. This function is used to create a round key for each round.
For each set of 4 words, a list with them is created. Looping through the words to be created, 
rotWord, subWord methods are called for the previous list entry, called temp, depending on the 
iteration index. 

If the index is a multiple of nk, then the rotword and subword methods are called on temp. The 
result of this is used to perform an XOR operation with the Rcon value stored at the 
(index divided by nk) position. 

Else, if the value of nk is greater than 6 and the index is a multiple of nk, only the subWord 
function is called on temp. 

Lastly, and XOR operation of temp and the word at nk position's earlier (nk - index) is computed
and is stored as the word which is returned. 

- rotWord: Accepts a word and returns a rotation of the bytes.

- subWord: Accepts a word and returns the result of an SBOX table to each byte in the word.

- invCipher: Inverse of cipher.

- invShiftRows: Inverse of shiftRows.

- invSubBytes: Inverse of subBytes.

- invMixCols: Inverse of mixCols.

