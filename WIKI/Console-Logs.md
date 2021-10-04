# Logs

Appropriate logging can be be viewed as an art. Logs should be concise and clear and be understandable
to newbie users as well as be useful to experienced users. Some of the logs may seem cryptic at first
but an effort was made to make them intuitive.

The application's sensitivity to CPU usage was a fcactor in log design. Perodic logs are not schedule
driven, the are opportunistic in that they look for opportunities to generate log with minimal impact.
Conditional logs are avoided in high running code where testing the condition every iteration adds CPU overhead.

The information is dense, the logs contain a lot of information in a small report. Every piece of data
has a puspose. Many fields can be correlated with other logs and fields and external data (pools, blockchain
explorers, etc) to provide a more complete picture of actual mining performance.

The descriptions below will help understanding the significance of each field and how it can be used
to assess mining performance, help identify problems and aid troubleshooting. 

Raw data is either provided by the server or by measured observations (ie time). All estimates and performance measuremnents are believed to be statisitcally accurate except as noted, however no formal statistical proof
has been done.

For users not interested in details the --quiet option can be used to suppress many of them.

### Colours

Somes logs and fields are colour coded based on their purpose or to highlight important information

* Blue is used to report networking events, primarilly new jobs.
* Green is used to identify when a share has been accepted.
* Yellow is used when a share is stale or to warn of a minor error that may affect performance.
* Red is a more serious error and is used when a share is rejected or when performance is affected or
  may cause the miner to stop mining and exit.

Coloured output can be disabled with the --no-color option. Note U.S. spelling.

## Start up

The startup messages are very important to ensure best performance. In particular
the features lists ensure the software is built for the CPU architecture and can
take advantage of all the optimizations.


`CPU: AMD Ryzen 7 1700 Eight-Core Processor`

`SW built on Feb  8 2020 with GCC 7.4.0`

`CPU features:  AVX2    AES SHA`

`SW features:   AVX2    AES SHA`

`Algo features: AVX512 VAES`

`Starting miner with AVX2 AES...`

* Line 1: CPU brand and model
* Line 2: Software build details
* Line 3: CPU hardware extensions available
* Line 4: Extensions supported by the software build
* Line 5: Extensions supported by the mining algorithm
* Line 6: Extensions to be used in the current session 

Hardware extensions are divided into 3 classes, SIMD, AES and SHA. Each class can have multiple levels.
Each provides additional CPU instructions and more complex operations. Only the highest ranked feature in
each class is displayed although lower ranking features may also exist and be avaiable. Not all features
have significant improvements and not all combinations are avalable in binary format for Windows.

### SIMD vector support
* SSE2: Minimum for 128 bit integer vector support, first available on Intel Core2. 
* SSSE3: Some additional instructions for 128 bit integer vectors, not very significant.
* SSE4.2: More 128 bit vector instructions, also not very significant.
* AVX: Supersedes all SSE versions. Provides initial support for 256 bit vectors but not integer support.
* AVX2: Minimum level for 256 bit integer vector support, first available on Intel Haswell and AMD Ryzen.
* AVX512: A suite of seperate extensions that provide 512 bit vector support. Cpuminer-opt support requires 
AVX512F, AVX512VL, AVX512DQ, & AVX512BW, first available on Intel Skylake X HEDT CPUs.

### AES encryption and decryption, aka AES_NI
* AES: Basic hardware AES support performs AES operations with a single instruction, also requires SSE2,
  first available on Intel Westmere.
* VAES: Vectored AES, supports 4 parallel AES operations with a single instruction, first available on Intel
           Icelake. Also requires AVX512F. AMD Zen3 supports 256 bit VAES only (2-way parallel) without AVX512F.
           The Intel specification states 256 bit VAES is only avaiable on CPUs with AVX512VL & AVX512F.

### SHA encryption and decryption, aka SHA_NI
* SHA supports basic SHA-256 operations with a single instruction, also requires SSE2. First available
      on Intel Goldmont but not widely avaiable until AMD Ryzen and Intel Icelake.

Some other messages are displayed based on options such as stratum connection, API, CPU affinity etc.

The number of threads and CPUs is displayed as:

`[THREADS] of [CPUS] miner threads started using [ALGO] algorithm`

The CPU affinity map displays in graphical form which logical CPUs are selected for mining. This is
only displayed when using --threads or --cpu-affinity. The CPUs are listed from left to right starting with
CPU 0 up to the number of logical CPUs available. A '!' indicates the CPU is selected for mining, a '.' if not. Example using --threads 4 --cpu-affinity 0x55 on an 8 core CPU:  [!.!.!.!.]

## New Stratum Diff, Block, Work report

This report is issued when a new job is received from the stratum server.
If the report is also for a new block or a changed stratum difficulty the report
will include new estimates. The block number can be used with data from other sources to identify the
coin, block reward, and exchange rate, all useful in determining profitability


`[2020-02-07 23:38:22] New Block 1293839, Job 1d9f`

`                      Diff: Net 50648, Stratum 26, Target 0.10156`

`                      TTF @ 2758.00 kh/s: block 2y2d, share 2m38s`

`                      Net TTF @ 1040.82 Gh/s: 3m29s`



There are 3 forms of this report. New job is a one line report, new block and new stratum diff are up to
4 lines. A new stratum diff also includes a new block and a new block includes a new job.

Line 1 (blue) shows what new information was received from the stratum server.

Stratum difficulty is used to moderate the share submission rate of miners with vastly different performance levels. This value affects the target hash to make it easier or more difficult to find a share. The stratum
difficulty also affects the value of each share which results in the same effective hash rate. Changing
stratum difficulty can affect the accuracy of session statistics as thy are based on the mean difficulty
for the session. Five minute stats are not affected because a new period is started with every stratum
difficulty change.

The block height (number) can be used in a multipool to identify the coin being mined. The block height
is also reported in other logs to help track performance.

Job id is also reported in other logs to help track performance and troubleshoot stale shares.

Line 2:

* Network difficulty, provided by the server, the minimum difficulty hash required to solve a block.
* Stratum difficulty, provide by the server, determines the value of a share and, combined with a target
factor (hard coded for each algo) determines the minimum share difficulty.
* Target difficulty, the minimum acceptible share difficulty, calculated from data received from the stratum
server.

Line 3:

* TTF, an abbeviation for Time To Find, an estimate of the average time required to find either a block or a share for a given hash rate.
* Reference hash rate of the miner, this is the traditional hash rate displayed by most miners. It is
calculated by counting the nonces hashed over time. It should be statistically equal to the sum of the
effective hash rate and lost hash rate. The reference hash rate is used to calculate TTF.
* Block TTF estimate calculated from miner's reference hash rate and network difficulty.
* Share TTF estimate calculated from miner's reference hash rate and target difficulty.

Line 4, only displayed in single coin pools

* network hashrate estimate is calculated from network difficulty and the number of blocks found during
  the mining session. This is an estimate of what the nominal hash rate would be based on the current
  difficulty and the block emission rate. It is not considered accurate.

### Extranonce2 report

`[2020-06-03 22:35:20] Extranonce2 02000000, Block 1233144, Net Diff 990.47`

The Extranonce2 report indicates an increment of the extranonce. This occurs with high hash rate
algorithms that can exhaust the nonce range before new work is received from the stratum server. Extranonce
allows hashing to continue uninterrupted. The Extranonce Subscription feature, aka xnonce or ES, is available
on most stratum pools and is enabled by default. Extranonce can be manually disabled using the --no-extranonce
option. Display of the Extranonce2 report can be suppressed using the --quiet option. 

### GBT/Getwork New block, Work report

`[2020-02-28 10:54:02] New block 6273226, Net Diff 118.48, Ntime 5e5c2e79`

`                      Miner TTF @ 3938.69 kh/s 2h04m, Net TTF @ 68.24 Mh/s 6m11s`

The GBT or getwork New Block report is similar to the stratum New Block with a few differences. There is no
stratum difficulty and no job id. New work is detected by a change of the ntime field of the block header data.

All present fields have the same meaning as the stratum log but some are calculated differently. Unlike the
stratum version the network hashrate is not an estimate. It is accurate as it is provided by the server using
the getmininginfo method. 

There is no target diff and no share TTF. The block TTF is displayed for the miner and network based on the
network difficulty and miner's reference hash rate or network hash rate, respectively. 
 

## Share submitted report


`[2020-03-01 16:52:18] 168 Submit diff 2.4496e-07, block 440815, job 3f4a`


The main purpose of the share submitted report is to timestamp the event to measure latency. It also contains
info to help tracking. The diff, block, and job, as well as the share counter are also reported in the share
result log

* submit count is a simple counter that is incremented every time a share is submitted. It should always
match up with a share result counter.

* The difficulty of the submitted share, should be less than or equal to the target difficulty
to be accepted, otherwise it will be rejected as a low difficulty share. Low difficulty shares are usually
discarded by the miner and not submitted. Submitting a low difficulty share is caused by using the wrong
algorithm or wrong pamameters, a pool misconfiguration or a bug in cpuminer-opt. A bug is more likely with
new algorithms or recently changed code.

* The current block being mined, also known as height.
 
* The job id, stratum only, useful to troubleshoot stale shares.


## Share result report

This report is generated when the pool's reply has been received acknowledging the submitted share.


`[2020-02-07 23:59:03] 5 Accepted 5 S0 R0 B0, 400.092 sec (159ms)`

`                      Diff 0.11393 (0.0853), Block 1044635, Job 1dd6`


Line 1:

* Share result count, simple counter independant of submit counter but they should match. A mismatch indicates
submitted shares without replies. The result count should also equal to the sum of accepted, stale and rejected
shares, collectivley known as replied or acknowledged shares.

* (green)"Accepted", (yellow)"Stale", (red)"Rejected", (magenta)"BLOCK SOLVED" counters. The currently
incremented counter is coloured appropriately and displayed in long form. The other counters will be
displayed in their short form with no colouring. Solved blocks also count as accepted therefore the accepted
count will also be incremented and displayed in green but in abbreviated form.

Stale shares can't be completely avoided. There is always a window beetween a job expiring and the miner
getting the new data. Id a share is submitted in this window it will be rejected as stale. Stale shares are
included in lost hash rate.

Rejected shares should never happen. They are either wrong algorithm, wrong parameters or a software bug.
Most common reject reasons are invalid job id which are reported as stale, low difficulty share and invalid
share. Invalid shares are caused by incorrect algorithm, wrong algorithm paramaters or a software bug.
Low diffiulty shares can be due to incorrect algorithm parameters, particularly for algos like yespower,
or a stratum mismatch due to a pool misconfiguration or software bug. When troubleshooting low difficulty
shares the share difficulty and the target difficulty from the new block log are useful information.

* The submit time in seconds since the last share. Determines share rate (shares/minute) which combined with
the stratum difficulty determines the effective hash rate. The share rate is used by pools to calculate
hash rate.

* (Latency ms) Time in milliseconds from share submission to reply, including transmission time and processing
at either end.

Line 2:

The second line of the Share Result report is displayed only if the share is not accepted or if --debug
option is used. Additional information is also displayed if the share was rejected to assist in troubleshooting.

* Share difficulty. The share difficulty does not matter most of the time so is FYI. As long as the share
difficulty is higher than or equal to the target difficulty all shares are considered equal by the
pool server based on the stratum difficulty.

* (share ratio) the fraction of the net difficulty required to solve a block, 1.0 or greater solves a block. 
Mostly FYI except when solo mining. It is the ratio of share difficulty over network difficulty.

* The current block height (block number), coloured magenta when the block is found by the miner.

* Job id, stratum only, the id of the job associated with the acknowleged share, coloured yellow if the
job was stale. This is obtained from data collected at submit time. 


## Periodic summary report


` [2020-03-01 16:49:10] scrypt: scryptn2.mine.zergpool.com:3435`

`                      Periodic Report     3m45s        14m58s`

`                      Share rate        9.84/min     9.68/min`

`                      Hash rate         27.03h/s     29.17h/s   (22.83h/s)`

`                      Lost hash rate        0h/s       .17h/s`

`                      Submitted            37          145`

`                      Accepted             37          144`

`                      Stale                 0            1`


Generated aproximately every 5 minutes. The timing is not precise because it is an opportunistic report.
It is produced when it is convenient in order to miminize the impact on mining.

The report contains data for the 5 minute interval in the left column, and session statistics in the right.

* Line 1: (blue) the current algorithm and URL

* Line 2: Sample time for current period and session Actual interval usually between 5 & 6 minutes.

* Line 2: Number of shares submitted per minute. Determines effective hash rate, the same way pool's
calculate hash rate. The five minute share rate is precise but the session rate may be uinaccurate if the
stratum difficulty has changed during the session. The reported session share rate is based on the time
weighted mean stratum difficulty.

* Line 3: Effective hash rate calculated from share rate and stratum difficulty. Should closely follow the
pool's reported hash rate.


Effective hash rate discounts rejected shares which are reported as lost hash rate.
The effective hash rate is the real performance of the miner once luck is factored in. It is also most
closely correlated with the pool's reported hash rate. The effective hash rate will fluctuate above and
below the miner reference hash rate according to luck (ie share rate). A low share rate will increase
volatility. Over time the mean effective hash rate should converge to the miner's reference hash rate
if everything is normal.

* line 4: Optional lost hashrate, the sum of the effective hashrate of stale and rejected shares. Only
displayed if not zero. Add this to effective hash rate for performance comparison with reference hash rate.
 
* Line 5: Number of shares submitted.

* Lines 6+: Optional number of stale or rejected shares or solved blocks, only displayed if not zero.

* Last Line: highest and lowest accepted share during the session. The lowest share should aproach the
target diff over time. If it does not it likely affects performance and should be reported. The highest
share is FYI and provides perspective on solo mining expectations. The lowest share is reset when stratum
difficulty is increased to avoid reporting a share with diff lower than the current target.


## CPU temperature and frequency report

Another opportunistic report to avoid interrupting mining operation. Temperature reports are dsplayed more
frequently at higher or rising temperatures and colour coded to draw attention.

* temp >= 80: 30+ seconds, red
* 70 <= temp < 80: 60+ seconds, yellow
* temp < 70: 120+ seconds, no colour

The sampling is approximately every 30 seconds and is the temperature of core 0 at that time. It is not an
average or absolute maximum, just a single sample. It does not in any way account for fluctuations or spikes
during the 5 minute period. The reported maximum is the previous highest sampled temperature.
