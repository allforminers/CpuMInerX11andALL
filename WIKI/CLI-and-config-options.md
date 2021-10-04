  -N, --param-n         N parameter for scrypt based algos

  -R, --param-r         R parameter for scrypt based algos

  -K, --param-key       Key (pers) parameter for algos that use it

  -o, --url=URL         URL of mining server

  -O, --userpass=U:P    username:password pair for mining server

  -u, --user=USERNAME   username for mining server

  -p, --pass=PASSWORD   password for mining server

      --cert=FILE       certificate for mining server using SSL

  -x, --proxy=[PROTOCOL://]HOST[:PORT]  connect through a proxy

  -t, --threads=N       number of miner threads (default: number of processors)

  -r, --retries=N       number of times to retry if a network call fails
                          (default: retry indefinitely)

      --retry-pause=N   time to pause between retries, in seconds (default: 30)

      --time-limit=N    maximum time [s] to mine before exiting the program.

  -T, --timeout=N       timeout for long poll and stratum (default: 300 seconds)

  -s, --scantime=N      upper bound on time spent scanning current work when
                          long polling is unavailable, in seconds (default: 5)

      --randomize       Randomize scan range start to reduce duplicates

      --reset-on-stale  Workaround reset stratum if too many stale shares

  -f, --diff-factor     Divide req. difficulty by this factor (std is 1.0)

  -m, --diff-multiplier Multiply difficulty by this factor (std is 1.0)

      --hash-meter      Display thread hash rates

      --coinbase-addr=ADDR  payout address for solo mining

      --coinbase-sig=TEXT  data to insert in the coinbase when possible

      --no-longpoll     disable long polling support

      --no-getwork      disable getwork support

      --no-gbt          disable getblocktemplate support

      --no-stratum      disable X-Stratum support

      --no-extranonce   disable Stratum extranonce support

      --no-redirect     ignore requests to change the URL of the mining server

  -q, --quiet           disable per-thread hashmeter output

      --no-color        disable colored output

  -D, --debug           enable debug output

  -P, --protocol-dump   verbose dump of protocol-level activities

  -S, --syslog          use system log for output messages

  -B, --background      run the miner in the background

      --benchmark       run in offline benchmark mode

      --cpu-affinity    set process affinity to cpu core(s), mask 0x3 for cores 0 and 1

      --cpu-priority    set process priority (default: 0 idle, 2 normal to 5 highest)

  -b, --api-bind        IP/Port for the miner API (default: 127.0.0.1:4048)

      --api-remote      Allow remote control

      --max-temp=N      Only mine if cpu temp is less than specified value (linux)

      --max-rate=N[KMG] Only mine if net hashrate is less than specified value

      --max-diff=N      Only mine if net difficulty is less than specified value

  -c, --config=FILE     load a JSON-format configuration file

  -V, --version         display version information and exit

  -h, --help            display this help text and exit
