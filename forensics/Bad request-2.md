# Bad request-2
Subdomains are encoded in base32, to decode them you also need to pad with the "=" char where required.\
Multiple entries will appear, some with "id:letter".\
All the entries without ":" are for misdirection.

Knowing this we can obtain the flag like so:

`decrypt.py`
```python
#!/usr/bin/env python3
import base64

messages = {}

def b32d(msg:str)->str:
    bmsg = msg.encode()
    bmsg = bmsg + b"="*(4 - len(bmsg)%4)
    return base64.b32decode(bmsg).decode()

with open("out.txt","r") as df:
    data = df.read()

for d in data.split('\n'):
    if d:
        m = b32d(d)
        if ":" in m: # only the valid entries have the ":"
            mid,mch = m.split(":")[0:2]
            messages[mid] = mch

for k in range(0, len(messages)):
    print(messages[str(k)], end="")
print()
```
So we just need to run:
```shell
~$ tshark -nr exfil.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name | sort | uniq | cut -d '.' -f 1 > out.txt
~$ python3 -BO decrypt.py
```