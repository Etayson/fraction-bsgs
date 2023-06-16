# fraction-bsgs (windows only)
This programm based on bsgs-cuda engine.  
BSGS algorithm is very good at not big search ranges.  
Therefore, with large search intervals, people use pollard-kangaroo or bsgs with random search and the like.  
Usually, a large range is divided into smaller ones and they are examined in a random order.  
Sometime randomly searching subranges overlapping alredy searched subranges.  
Therefore, a different approach is proposed, where overlap is impossible and at the same time it is not required to store information about already explored subranges.  
Let's call it *fraction*.  
To work, we need the desired public key and the search ranges As well as a divisor, which indicates how many times we will reduce the search area and public key.  
For example, we have a public key in the range 2^69 bits. We can divide the range and the public key, for example, by 64(2^6).  
Now we will have a search range of 69-6=63 bits and the public key divided by 64. Since the desired public key is unlikely to be completely divisible by 64,
we must subtract 1/64(G),2/64(G)...63/64(G) from the received public key and compare result with hash table until this the public key becomes an hash tables.  
When dividing by 64, only one public key out of 64 will lie in the range we need.  
Which one is unknown, so we can say that this approach is similar to a random search.  
Derivative public keys from the desired are made on the fly and are not repeated in cycle. That is why there is no need to store already researched.  
You can also save your work, i.e. number in the division cycle or start work from the desired number, optionally from the beginning.  
For ex. with puzzle#70(69bit) and -dbit 6 divisor 2^6=64 search range 2^63 result will be in pos 49 of 63  
With puzzle#80(79bit) and -dbit 7 divisor 2^7=128 search range 2^72 result will be in pos 0 of 127 (very lucky divisor and number)  

In simple numbers how it work. For ex.  our desired key is 379 that lie in range 2^9  
We want search in range 2^6=64, so -dbit 3 and divisor is 2^3=8  
Devide 379/8 = 47,375  
fraction = 1/8 = 0,125  
our hash table for ex contain only 64 numbers 1..64  
Stage 0 compare 47,375 with HT => result negative  
Stage 1  subtract from it 0,125 => 47.25, compare with HT => result negative.  
Stage 2  subtract from it 0,125 => 47.125, compare With HT => result negative.  
Stage 3  subtract from it 0,125 => 47, compare With HT => result positive.  
So now we need multiply 47 by divisor 2^3=8 = 376 and add stage number =>376 +3 = 379  
Desired key 379 founded in 3 stage of 8  

This is not a magic thing or so just a different way of looking at random search, in which you can save, continue working from the same place and also start it from other.  
And in big search ranges it can take a long-long time to find depending on luck. 
```
Usage:
-t       Number of GPU threads (must be a multiple of 32)
-b       Number of GPU blocks (must be a multiple of 2 and SM count)
-p       Number of pparam (must be a multiple of 2)
-d       Select GPU IDs (coma separated) (ex. -d 0,1,2)
-pb      Set single uncompressed/compressed pubkey for searching
-pk      Range start from
-pke     End range
-w       Set number of items in HashTable 2^ or decimal representation (ex. -w 30 is the same as -w 1073741824)
-htsz    Set size of HashTable 2^ (ex. -htsz 27)
     Recommendation:
     with htsz 27 value -w should be less Or equil To 1331331443 Or 2^30.310222637591963
     with htsz 28 value -w should be less or equil to 1777178603 Or 2^30.726941530690112
     with htsz 29 value -w should be less or equil to 3069485950 Or 2^31.515349920643907
     with htsz 30 value -w should be less or equil to 3069485951 Or 2^31.515349920643907
     with htsz 31 value -w should be less or equil to 3069485951 Or 2^31.515349920643907       
-wl       Set recovery file from which the state will be loaded
-wt       Set timer for autosaving current state
-dbit     Set number of divisor 2^ (ex. -dbit 6 mean divisor = 2^6 = 64)
-pos      Set number of pos in division cicle in hex format (ex. -pos FF)

Good value for:
GTX1660super -htsz 28 -w 29.87 -t 256 -b 88  -p 130
RTX2080ti    -htsz 29 -w 30.5  -t 256 -b 272 -p 220
RTX3070      -htsz 28 -w 30.25 -t 256 -b 138 -p 244
RTX3090      -htsz 30 -w 31.51 -t 512 -b 328 -p 280 Note: requre a lot of host RAM for generating files
Example:
FractionBSGS1_9_7F.exe -dbit 6 -htsz 28 -w 29.87 -t 256 -b 88 -p 130 -pk 8000000000000000 -pke ffffffffffffffff -pb 03100611c54dfef604163b8358f7b7fac13ce478e02cb224ae16d45526b25d9d4d
```
HashTable files are compatible with bsgs-cuda  
Purebasic v.5.31 required for compilation  
