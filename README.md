# NTPD monitoring with ELK

How to monitor NTPD server with ELK

## Activate ntpd logs

Update the ntpd configuration

      vim /etc/ntp.conf
      statsdir /var/log/ntpstats/
      statistics peerstats sysstats
      filegen peerstats file peerstats.log type day enable
      filegen sysstats file sysstats.log type none enable
 
Restart the daemon

      systemctl ntpd restart
      
### peerstats 

Record peer statistics. Each NTP packet or reference clock update received appends one line to the peerstats

| Units | Description | 
|-------|-------| 
| MJD | date | 
| s | time past midnight | 
| IP | source address | 
| hex | status word | 
| s | clock offset | 
| s | roundtrip delay | 
| s | dispersion | 
| s | RMS jitter | 

    tailf /var/log/ntpstats/peerstats
    58927 1293.735 91.224.149.41 941a -0.031646296 0.047783430 0.019218732 0.002787419
    58927 1315.710 129.250.35.250 945a -0.033762728 0.027946319 0.019098537 0.001580388
    58927 1848.736 193.141.27.6 941a -0.034690903 0.054624853 0.015236537 0.001218647
    58927 2006.714 92.222.117.115 961a -0.033217220 0.031581741 0.015059831 0.001349028
  
### sysstats

Each hour one line is appended to the sysstats file set in the following format

| Units | Description |
|-------|-------------|
| MJD | date |
| s  | time past midnight |
| s  | time since reset |
|  | packets received |
| | packets generated |
| | current versions |
| | old version |
| | access denied |
| | bad length or format |
| | bad authentication |
| | declined |
| | rate exceeded |
| | kiss-o'-death packets sent |
 
     tailf /var/log/ntpstats/sysstats
     58927 52035.251 3599 225 205 225 0 0 0 0 0 0 0
     58927 55635.257 3600 88 88 88 0 0 0 0 0 0 0
     58927 59235.313 3600 49 49 49 0 0 0 0 0 0 0
  
## Counting NTP clients

### logs results

      [root@localhost ntpstats]# ll
      total 740
      -rw-------. 1 root root 669741 Mar 22 08:42 iptables.log
      -rw-r--r--. 2 ntp  ntp    3549 Mar 22 07:14 peerstats.log
      -rw-r--r--. 1 ntp  ntp   32829 Mar 21 00:58 peerstats.log.20200320
      -rw-r--r--. 1 ntp  ntp   16910 Mar 22 00:59 peerstats.log.20200321
      -rw-r--r--. 2 ntp  ntp    3549 Mar 22 07:14 peerstats.log.20200322
      -rw-r--r--. 2 ntp  ntp     347 Mar 22 08:35 sysstats.log
      -rw-r--r--. 1 ntp  ntp     980 Mar 21 00:46 sysstats.log.20200320
      -rw-r--r--. 1 ntp  ntp    1041 Mar 22 00:35 sysstats.log.20200321
      -rw-r--r--. 2 ntp  ntp     347 Mar 22 08:35 sysstats.log.20200322

