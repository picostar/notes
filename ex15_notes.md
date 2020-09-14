signal level AT+CSQ
network registration status AT+CREG?
list all networks avail AT+COPS=?   ERROR? 

Check Current Network AT+COPS?
de register from network before registering to a new network AT+COPS=2
register T-MO   AT+COPS=1,2,"310260"
register ATT  AT+COPS=1,2,"310410"
register Verizon  AT+COPS=1,2,"311480"

AT+CPOL=1,2,"310260"


311480



# mmcli -m 0 --3gpp-scan --timeout=300
  ---------------------
  3GPP scan | networks: 311882 - 311 882 (lte, available)
            |           312530 - 312 530 (lte, available)
            |           310120 - Sprint (lte, available)
            |           310410 - AT&T (lte, current)
            |           313100 - 313 100 (lte, available)
            |           310260 - T-Mobile (umts, available)
            |           310410 - AT&T (umts, available)
            |           311480 - Verizon (lte, available)
            |           311490 - 311 490 (lte, available)
            |           310260 - T-Mobile (lte, available)
