# Intruder

### Task 2 What is Intruder?
Burp Suite's built-in fuzzing tool. Allows you to take a request (usually captured in the Proxy before being passing to the intruder) and use it as a template to send many more requests with slightly altered values automatically. Functionality is very similar to command-line tools such as `Wfuzz` or `ffuf`

In community edition, the intruder speed is nerfed, so you will need Professional version to unlock the full speed.

Intruder interface:
- **Positions**: allows you to select an Attack Type
- **Payloads**: select values to insert into each of the positions you define in the previous tab
- **Resource pool**: divide your resources between tasks.
- **Options**: configure the attack behavior

### Task 3 Positions

Before conducting the attack, need to set the *positions*.

Burp will attempt to determine the most likely places we may wish to insert a payload automatically -- these are highlighted in green and surrounded by silcrows (`§`)

- **Add**: let us define new positions by highlighting them in the editor and clicking the button
- **Clear**: removes all defined positions, leaving us with a blank canvas to define our own.
- **Auto**: attempts to select the most likely positions automatically; this is useful if we clered the default positions and want them back.

### Task 5 Sniper
The first and most common attack type.

When conducting a sniper attack, we provide *one* set of payloads. For example, this could be a single file containing a wordlist or a range of numbers. From here on out, we will refer to a list of items to be slotted into requests using the Burp Suite terminology of a "Payload Set". Intruder will take each payload in a payload set and put in into each defined position in turn.

Good for attacks where you're attack a single position

### Task 6 Battering Ram
Like Sniper, Battering Ram takes *one* set of payloads (e.g. one wordlist). *Unlike* Sniper, the Battering Ram puts the sam payload in *every* position rather in each position in turn.

Ex) username=admin&password=admin ... etc.

### Task 7 Pitchfork
It is like having multiple Snipers running simultaneously. Pitchfork uses one payload set per position (up to a maximum of 20) and iterates through them all at once.

Bruteforce example, need two wordlists:
- First wordlist will contain usernames.
- Next a password list

Matches up each entry in the first list with the second list.

### Task 8 Cluster Bomb
Allows you to choose multiple payload sets: one per position, up to a maximum of 20; however, whilst Pitchfork iterates through each payload simultaneously, Cluster bomb iterates through each payload set individually, making sure that every possible combination is tested.

For example with usernames and passwords, every password would be tried with every single username.

### Task 9 Payloads
- The **Payload Sets** section allows you to choose which position we want to configure a set for as well as what type of payload we would like to use.
	- With Sniper or Battering Ram, the dropdown menu "Payload Set" will only have one option, regardless of how many positions we have defined.
	- if using an attack with multiple sets, then there will be one item in the dropdown for each position.
- Second dropdown menu allows you to select a "payload type". By default this is "simple list" -- which, as the name suggests,lets you load in a wordlist to use. There are many other payload types available -- some common ones include: `Recursive Grep`, `Numbers` and `username generator`.


- **Payload options**: differ depending on the payload type we select for the current payload set.
- **Payload Processing**: define rules to be applied to each payload in the set before being sent to the target.
- **Payload encoding**: ex) base64, urlencoded, etc.


### CSRF Token Bypass

Catching the request to `http://<ip>/admin/login/`

We see that there is a login token value for a hidden `<input>` form html tag and a session cookie that changes for every request, which means we need these two values each time we make a request.

Enter: Macros

In many cases, we could do this kind of thing using payload type called "Recursive Grep", which would be a lot easier than what we're going to h ave to do here. Unfortunately, because the web app redirects us back to the login page rather than simply showing us both of our target parameters, we will need to do this the hard way. Specifically, we will have to define a "macro" to be executed before each request. This will grab a unique session cookie and matching login token, then substitute them into each request of our attack.

In this case, we simply want to send a GET request to `/admin/login`.

Fortunately, this is a very easy process.

- Project Options -> Sessions
- Macros section and click Add
- Give macro a suitable name
- Sessions -> Session Handling Rules -> Add new rule
- Fill in appropriate description

Rule Description for macro defined
- Select "Update only the following parameters", then click "Edit" button
- In the "Enter a new item" text field, type "loginToken"
- Select "update only the following cookies", click "edit"
- Enter "session"

Now you can run the attack from the intruder