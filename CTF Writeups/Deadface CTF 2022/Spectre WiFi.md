# Solution

- This actually requires a bit of OSINT: what is the format of a default netgear router?
- Huh, look at what's on ghosttown: https://ghosttown.deadface.io/t/when-will-people-learn-about-defaults/79 ([web archive link](https://web.archive.org/web/20221016010535/https://ghosttown.deadface.io/t/when-will-people-learn-about-defaults/79))
- It leads to a thread https://hashcat.net/forum/thread-4463.html that indicates that the default password is formatted using adjective + noun + 3 decimal numbers. Unfortunately the link in the post for the wordlist is no longer active, so we'll have to build our own.
- We need a list of nouns, adjectives, and numbers. Numbers is easy enough, a simple python script can do it: `for i in range(1000): print('%03d' % i)`
    - Don't forget about leading zeros!
- Adjectives and nouns can be more difficult, but we can find lists online. In fact, I came across https://github.com/soxrok2212/PSKracker/tree/master/dicts/netgear-spectrum during the CTF competition and used this list with the pre-combined adjectives and nouns
- Now to crack the password. Since I don't want to combine the lists ahead of time, we can use hashcat and its [combiner attack mode](https://hashcat.net/wiki/doku.php?id=combinator_attack), or `-a 1`. You might be used to `-a 0`
- `hash-identifier` or any online hash identifier should be able to tell you this is a SHA256 hash, or you might just know from experience. Looking it up either in the hashcat help text or on https://hashcat.net/wiki/doku.php?id=example_hashes, `SHA2-256` with mode 1400 is what we want
    - Final command: `hashcat -a 1 -m 1400 hash.txt netgear-spectrum.txt numbers.txt --session "spectre"`
- Run this, and you get the original password very quickly

# Original CTF Challenge

A NETGEAR router in the main library of Eastern State University was hacked, leading to a prolonged man-in-the-middle attack and student passwords getting swiped. The network admin cannot remember how to log into the router’s admin portal to change the password and review the logs. He does not remember the WPA key to log in. He also thinks the default wpa keys were never changed. Here was the hash he found:

4be29bb887c260061afb229d81d250a05f92fa59e324cc16fa7ae2fd3fadd1a9

Can you find out what the wpa keys were? Assume the key is not salted due to the age of the NETGEAR router.

Enter the answer as flag{password}.