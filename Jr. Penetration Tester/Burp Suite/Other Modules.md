# Other Modules
Covers the *Decoder, Comparer* and *Sequencer* tools.

### Task 2 Decoder Overview
Allows you to manipulate data. As the name suggests, we can *decode* information that we capture during an attack, but we can also *encode* data of our own, ready to be sent to the target. Decoder also allows us to create *hashsums* of data, as well as providing a Smart Decode feature which attempts to decode provided data recursivley until it is back to being plaintext. (like the "Magic" function of Cyberchef)

Options:
1. Text area is where the text to be encoded or decoded goes
2. Can treat the input as text or hexadecimal byte values at the top of the list on the right.
3. Further down the list, there's dropdown menus to *encode, decode, or hash* the input.
4. "Smart Decode" feature, which attempts to decode the input automatically

### Task 3 Encoding/Decoding
Taking a closer look at the manual encoding and decoding options. These are the same whether we choose the decoding or encoding menu:

- Plain
- URL
- HTML
- Base64
- ASCII Hex
- Hex
- Octal
- Binary
- Gzip

### Task 4 Hashing
Select the *Hash* dropdown menu to choose the has to use.

Input the text to be run through the hashing algorithm

### Task 5 Comparer
Allows you to compare two pieces of data, either by ASCII words or by bytes

The interface:
1. When loadig data into Comparer, it will appear as rows in these tables -- then you should select two datasets to compare
2. Upper right, there's options for pasting data in from the clipboard (paste), loading data from a file (load), removing the current row (Remove) and clearing all datasets (clear)
3. Bottom right, option to compare datasets by either words or bytes

### Task 7 Sequencer
Allows you to measure the *entropy* (randomness, in other words) of "tokens" -- strings that are used to identify something and should, in theory, be generated in a cryptographically secure manner. For example, we may wish to analyze the randomness of a session cookie or a Cross-Site Request Forgery (CSRF) token protecting a form submission. If it turns out that these tokens are not generated securely, then we can (in theory) predict the values of upcoming tokens. Just imagine the implications of this if the token in question is used for password resets...

Interface:
- **Live Capture**: the more common method -- is the default sub-tab for Sequencer. Live capture allows you to pass a request to Sequencer, which we know will create a token for us to analyze.
- **Manual load**: Allows us to load a list of pre-generated token samples straight into Sequencer for analyze. Using manual load we don't have to make thousands of requests to our target, but it does mean that we need to obtain a large list of pre-generated tokens



