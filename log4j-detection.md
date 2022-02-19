# log4j RCE Exploitation Detection

You can use these commands and rules to search for exploitation attempts against log4j RCE vulnerability CVE-2021-44228

## Grep / Zgrep

This command searches for exploitation attempts in uncompressed files in folder `/var/log` and all sub folders

```bash
sudo egrep -I -i -r '\$(\{|%7B)jndi:(ldap[s]?|rmi|dns|nis|iiop|corba|nds|http):/[^\n]+' /var/log
```

This command searches for exploitation attempts in compressed files in folder `/var/log` and all sub folders

```bash
sudo find /var/log -name \*.gz -print0 | xargs -0 zgrep -E -i '\$(\{|%7B)jndi:(ldap[s]?|rmi|dns|nis|iiop|corba|nds|http):/[^\n]+'
```

## Grep / Zgrep - Obfuscated Variants

These commands cover even the obfuscated variants but lack the file name in a match. 

This command searches for exploitation attempts in uncompressed files in folder `/var/log` and all sub folders

```bash
sudo find /var/log/ -type f -exec sh -c "cat {} | sed -e 's/\${lower://'g | tr -d '}' | egrep -I -i 'jndi:(ldap[s]?|rmi|dns|nis|iiop|corba|nds|http):'" \;
```

This command searches for exploitation attempts in compressed files in folder `/var/log` and all sub folders

```bash
sudo find /var/log/ -name '*.gz' -type f -exec sh -c "zcat {} | sed -e 's/\${lower://'g | tr -d '}' | egrep -i 'jndi:(ldap[s]?|rmi|dns|nis|iiop|corba|nds|http):'" \;
```

## Log4Shell Detector (Python)

Python based scanner to detect the most obfuscated forms of the exploit codes. 

https://github.com/Neo23x0/log4shell-detector

## Find Vulnerable Software (Windows)

```powershell 
gci 'C:\' -rec -force -include *.jar -ea 0 | foreach {select-string "JndiLookup.class" $_} | select -exp Path
```

by [@CyberRaiju](https://twitter.com/CyberRaiju/status/1469505677580124160)


## YARA

https://github.com/Neo23x0/signature-base/blob/master/yara/expl_log4j_cve_2021_44228.yar

## Help 

Please report findings that are not covered by these detection attempts.

## Credits 

I got help and ideas from 

- [@matthias_kaiser](https://twitter.com/matthias_kaiser) 
- [@daphiel](https://twitter.com/daphiel)
- [@Reelix](https://twitter.com/Reelix)
- @atom-b

