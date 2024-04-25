# my poor git
Author: burturt (me!)
72 solves during CTF, 465 points

My poor git server! I think someone took a hammer to the server and ruined a few of the files!

The git repo is available at /flag.git

## Writeup
When making this challenge, I was inspired by the thought that git is a key-value store, such that every older commit object ID is included in the newer commit objects, so what happens if we delete one of the objects?
### Walking the happy path
- Let's try to visit the URL given:
![Screenshot 2024-03-04 at 6.35.30 PM.png](../_resources/Screenshot%202024-03-04%20at%206.35.30%20PM.png)
- Darn, not very helpful. What about /flag.git?
![Screenshot 2024-03-04 at 6.38.57 PM.png](../_resources/Screenshot%202024-03-04%20at%206.38.57%20PM.png)
- Ok, so at this point, we have nothing to go off of except that this is supposedly a git server. Well, what if we try to just git clone it like from github?
![Screenshot 2024-03-04 at 6.41.11 PM.png](../_resources/Screenshot%202024-03-04%20at%206.41.11%20PM.png)
- Well, git cloning didn't work using git. It also seems like it's the remote server that stops us from getting any data - our git client literally got nothing >:(
### Downloading git repo manually
- My first instinct was that there must be some URL paths that we can visit directly in order to download the objects. Above, we get what appears to be some commit IDs - git must actually receive these somehow right?
- 


# Challenge Details
Author: burturt (me!)
18 solves during the CTF, 492 points

Apparently my poor git server didn't like being called "dumb", so it disabled its dumb capabilities.

The git repo is available at /flag.git

# Writeup