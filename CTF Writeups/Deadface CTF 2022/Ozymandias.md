# Solution

- This is actually a fairly simple challenge, which can be solved using a lot of guess-and-check or just knowing that triple `=`s means some variant of base64, but I want to show a couple automated tools:

## CyberChef Magic!

- CyberChef Magic is actually amazing: all you need to do is copy your text into the cyberchef input window, add the magic recipe, and using text analysis, cyberchef tries to automatically de-encode your message!
- [See for yourself here!](https://gchq.github.io/CyberChef/#recipe=Magic%283,false,false,%27%27%29&input=SkJFRlFRM0NJRTNHNDIzS0lOWVRJTUtaSU5EWEFaM1ZPNVVFS1RTVVBGR0VFNkRITlZCVVEzM0xPVVpFMjRTMkdKRFZVNkNRSVpKV0NNWlJPUkZHU1VLR09VWldFNktSSkEzVU9TQ1lPTkFYRVNESkdGM1VFWkNUTkZJWFFTU1pMRVlWVTREWUdKSUhHMzNTTlpBVUVaRDJOTllVRVNUT01SRkZHNEpaTU5aRTRNU05PWTJVWVIzUEtSVVZTMkJZTEZTRzRNTFBORjJFRzNLQkpSTEdRT0NMT1pYREtTREpOWkdFSVpDVE9aVFdLNkRVSkJIRUU2TFFKWlpWUzRKUk5JWldRMzJITVZVWFM9PT0 "https://gchq.github.io/CyberChef/#recipe=Magic(3,false,false,'')&input=SkJFRlFRM0NJRTNHNDIzS0lOWVRJTUtaSU5EWEFaM1ZPNVVFS1RTVVBGR0VFNkRITlZCVVEzM0xPVVpFMjRTMkdKRFZVNkNRSVpKV0NNWlJPUkZHU1VLR09VWldFNktSSkEzVU9TQ1lPTkFYRVNESkdGM1VFWkNUTkZJWFFTU1pMRVlWVTREWUdKSUhHMzNTTlpBVUVaRDJOTllVRVNUT01SRkZHNEpaTU5aRTRNU05PWTJVWVIzUEtSVVZTMkJZTEZTRzRNTFBORjJFRzNLQkpSTEdRT0NMT1pYREtTREpOWkdFSVpDVE9aVFdLNkRVSkJIRUU2TFFKWlpWUzRKUk5JWldRMzJITVZVWFM9PT0")
- Note: when I used it, I had to remove the spaces and newlines originally in the challenge text for it to work

## Ciphey!

- [Ciphey](https://github.com/Ciphey/Ciphey) is like CyberChef magic but does significantly more analysis automatically. It can be slower since it goes into *much* further depth than cyberchef magic, but is a good tool to have.
- [Follow the instructions here to install](https://github.com/Ciphey/Ciphey/wiki/Installation), them simply do `CIPHEY_COMMAND -t "text"` and wait. That's it! No need to remove spaces or anything
    - Ciphey command will depend on your installation method
	- Do NOT use the docker option on arm / M1 apple devices. It's suuuuuppppperrr slow.

# Original CTF Challenge

Ozymandias, by PERCY BYSSHE SHELLEY

I met a traveler from an antique land, Who said—“Two vast and trunkless legs of stone Stand in the desert. . . . Near them, on the sand, Half sunk a shattered visage lies, whose frown, And wrinkled lip, and sneer of cold command, Tell that its sculptor well those passions read Which yet survive, stamped on these lifeless things, The hand that mocked them, and the heart that fed; And on the pedestal, these words appear:

JBEFQQ3CIE3G423KINYTIMKZINDXAZ3VO5UEKTSU PFGEE6DHNVBUQ33LOUZE24S2GJDVU6CQIZJWCMZR ORFGSUKGOUZWE6KRJA3UOSCYONAXESDJGF3UEZCT NFIXQSSZLEYVU4DYGJIHG33SNZAUEZD2NNYUESTO MRFFG4JZMNZE4MSNOY2UYR3PKRUVS2BYLFSG4MLP NF2EG3KBJRLGQOCLOZXDKSDJNZGEIZCTOZTWK6DU JBHEE6LQJZZVS4JRNIZWQ32HMVUXS===

Nothing beside remains. Round the decay Of that colossal Wreck, boundless and bare The lone and level sands stretch far away.”

Submit the flag as flag{flag text}.