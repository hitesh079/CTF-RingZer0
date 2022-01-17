# Challenge - VoiCI unE SUpeRbe reCeTtE cONcontee pAR un GrouPe d'ArtistEs culinaiRe, dONT le BOn Gout et lE SeNs de LA cLasSe n'est limIteE qUe par LE nombre DE cAlOries qU'ils PeUVEnt Ingurgiter. Ces virtuoses de la friteuse vous presente ce petit clip plein de gout savoureux !! We hijack the Bacon Bacon Truck in San Francisco! The cure for your worst Hangovers and more: Bacon True: Science says so

## My Approach:
I began the challenge by reading the text I could understand. Towards the bottom, the language used is in English. I knew that this wouldn't be the ciphertext as it is in plain text. Then I read, the first paragraph and the last line was in french. When I put the line in google for translation it translated to "These virtuosos of the fryer present you this small clip full of tasty taste!". This also couldn't be our ciphertext.
After this, I thought the first paragraph minus the last line must be the ciphertext. 
I went to https://cryptii.com/ because it has the online implementation of many ciphers. I used multiple online ciphers on this website but most failed to give me a proper solution. 
While going through the list of encryption methods on this site I found out something called bacon cipher. I read online about this cipher and thought this challenge was about bacon so let me go ahead and try it out.
I wrote a small snippet of code to convert the given text to A's and B's where A is a lowercase letter and B is an uppercase letter. This text is then converted to lowercase because cryptii needs lowercase characters to decode the ciphertext.

Once this was done, we could see the flag:

![bacon](https://user-images.githubusercontent.com/19536413/149803991-3a381b18-57e4-4075-9e07-abce2ca181e8.png)

#### Flag - baconandjackdaniels
