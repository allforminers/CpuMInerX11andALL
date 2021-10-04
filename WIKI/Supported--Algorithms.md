# Supported algorithms

                          allium        Garlicoin
                          anime         Animecoin
                          argon2        Argon2 coin (AR2)
                          argon2d250    argon2d-crds, Credits (CRDS)
                          argon2d500    argon2d-dyn,  Dynamic (DYN)
                          argon2d4096   argon2d-uis, Unitus, (UIS)
                          axiom         Shabal-256 MemoHash
                          blake         Blake-256 (SFR)
                          blake2b       Blake2b 256
                          blake2s       Blake-2 S
                          blakecoin     blake256r8
                          bmw           BMW 256
                          bmw512        BMW 512
                          c11           Chaincoin
                          decred
                          deep          Deepcoin (DCN)
                          dmd-gr        Diamond-Groestl
                          groestl       Groestl coin
                          hex           x16r-hex
                          hmq1725       Espers
                          hodl          Hodlcoin
                          jha           Jackpotcoin
                          keccak        Maxcoin
                          keccakc       Creative coin
                          lbry          LBC, LBRY Credits
                          luffa         Luffa
                          lyra2h        Hppcoin
                          lyra2re       lyra2
                          lyra2rev2     lyra2v2
                          lyra2rev3     lyrav2v3, Vertcoin
                          lyra2z
                          lyra2z330     Lyra2 330 rows, Zoin (ZOI)
                          m7m           Magi (XMG)
                          minotaur      Ringcoin (RNG)
                          myr-gr        Myriad-Groestl
                          neoscrypt     NeoScrypt(128, 2, 1)
                          nist5         Nist5
                          pentablake    Pentablake
                          phi1612       phi
                          phi2          Luxcoin (LUX)
                          phi2-lux      identical to phi2
                          pluck         Pluck:128 (Supcoin)
                          polytimos     Ninja
                          power2b       MicroBitcoin (MBC)
                          quark         Quark
                          qubit         Qubit
                          scrypt        scrypt(1024, 1, 1) (default)
                          scrypt:N      scrypt(N, 1, 1)
                          sha256d       Double SHA-256
                          sha256q       Quad SHA-256, Pyrite (PYE)
                          sha256t       Triple SHA-256, Onecoin (OC)
                          sha3d         Double keccak256 (BSHA3)
                          shavite3      Shavite3
                          skein         Skein+Sha (Skeincoin)
                          skein2        Double Skein (Woodcoin)
                          skunk         Signatum (SIGT)
                          sonoa         Sono
                          timetravel    Machinecoin (MAC)
                          timetravel10  Bitcore
                          tribus        Denarius (DNR)
                          vanilla       blake256r8vnl (VCash)
                          veltor        (VLT)
                          whirlpool
                          whirlpoolx
                          x11           Dash
                          x11evo        Revolvercoin
                          x11gost       sib (SibCoin)
                          x12           Galaxie Cash (GCH)
                          x13           X13
                          x13bcd        bcd
                          x13sm3        hsr (Hshare)
                          x14           X14
                          x15           X15
                          x16r
                          x16rv2        Ravencoin (RVN)
                          x16rt         Gincoin (GIN)
                          x16rt-veil    Veil (VEIL)
                          x16s          Pigeoncoin (PGN)
                          x17
                          x21s
                          x22i
                          x25x
                          xevan         Bitsend (BSD)
                          yescrypt      Globalboost-Y (BSTY)
                          yescryptr8    BitZeny (ZNY)
                          yescryptr8g   Koto (KOTO)
                          yescryptr16   Eli
                          yescryptr32   WAVI
                          yespower      Cryply
                          yespowerr16   Yenten (YTN)
                          yespower-b2b  generic yespower + blake2b
                          zr5           Ziftr

## Scrypt, yescrypt, yespower parameters

Many variations of scrypt based algos can be mined by specifying their parameters even if not explicitly
defined in the miner. New variations that use a common base are automatically supported. The 3 bases are
yescrypt (aka yespower v0.5), yespower (aka yespower v1.0), yespower-b2b (yespower with sha256 replaced
with blake2b), and scrypt. 

Variations listed above may be mined using their unique name or by parameters. Below are some known algorithm
variations that are unlisted and must be mined using their parameters. Parameters can be obtained from the
coin's specifications, if the developpers chose to publish them, or buried in the wallet code if not.

A parameter is not required when the algorithm uses the default.

* -N --param-n: This optional parameter is sometimes specified using the suffix :N added to the algorithm
parameter, ie --algo scrypt:1048576. The default value of N if not specified is 1024.

* -K --param_key: This optional parameter is sometimes called "client key" or "pers" and is a string.
Some algorithms specify a key length but cpuminer-opt determines the length automatically. This can result
unexpected results if the length was set incorrectly in the specification, as noted in one instance below.
The default key if not specified is NULL.

* -R --param-r: Optional, the default if not specified is 1.

### Algorithms using scrypt parameters

* scrypt

`--algo scrypt`

Scrypt with no paraneters uses the default N=1024 as used by Litecoin.

* scryptn2

`--algo scrypt --param-n 1048576`

* yespower

`--algo yespower`

yespower with no parameters uses the defaults N=2048, R=32, Key=NULL.

* cpupower
 
`--algo yespower --param-key "CPUpower: The number of CPU working or available for proof-of-work mining"`

* sugarchain

`--algo yespower --param-key "Satoshi Nakamoto 31/Oct/2008 Proof-of-work is essentially one-CPU-one-vote"`

* yespoweriots

`--algo yespower --param-key "Iots is committed to the development of IOT"`

* yespowerlitb

`--algo yespower --param-key "LITBpower: The number of LITB working or available for proof-of-work mini"`

The key length in the LITB POW specification  was incorrectly set resulting the actual key being 2 characters
shorter than intended. The key parameter must be set as above, with the last 2 characters "ng" from the
specification truncated.

* yespoweric

`--algo yespower --param-key "IsotopeC"`

* yespowerurx

`--algo yespower --param-key "UraniumX"`

* yespowerltncg

`--algo yespower --param-r 32 --param-key "LTNCGYES"`

* yescrypt

`--algo yescrypt`

Yescrypt with no parameters uses the defaults N=2048, R=8, Key=NULL.

* power2b

`--algo yespower-b2b --param-key "Now I am become Death, the destroyer of worlds"`