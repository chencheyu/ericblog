---
date: 2019-01-04
publish: true
slug: 0df2d74a-7f9b-4490-b84d-a0a6b71df6c2
tags:
- CS/Algorithm
title: RSA.md
---
### 簡介

一個簡單的 RSA 實作

```python
import random
#des p,q
p=113
q=117

prim=[64513,64553,64577,65419,66301,65651,67139,67141,65537,66293,65687,66919,66347,65447]

def gcd(p,q):
  if (q == 0):
    return ( p, 1, 0 )
  d,b,a = gcd(q, p % q);
  b=b-(p//q)*a
  return (d, a, b)
  

def inverse(k,n):
    d,a,b= gcd(k, n);
    if (d > 1):
      print("Inverse does not exist."); return 0;
    if (a > 0): 
      return a;
    return n + a;

def keygen(p,q): 
  n=p*q
  oln=(q-1)*(p-1)
  e=prim[random.randint(0,len(prim))]
  d=inverse(e,oln)
  return (e,n),(d,n)

def decode(s):
  #english only
  ls=[]
  if len(s)%2==1:
    s+=' '
  for i in range(0,len(s),2):
    ls.append(ord(s[i])*0xff+ord(s[i+1]))
  return ls

def encode(ls):
  s=""
  for i in ls:
    s=s+chr(i//0xff)+chr(i%0xff)
  return s

def syc(m,e,n):
  c=[]
  for i in range(0,len(m)):
    c.append(pow(m[i],e,n))
    
  return c

def usyc(c,d,n):
  m=[]
  for i in range(0,len(c)):
    m.append(pow(c[i],d,n))
    
  return m

print(keygen(p,q))

c=syc(decode("nttucsie"),7237327049, 26217266885746803617)
m=usyc(c,4962162255038558585, 26217266885746803617)
print(encode(m))
```

keygen() 用以產生金鑰對，返回temp(公，私)，pq因大於10^10
sys() 給定encod過的串列及私鑰，返回加密串列，建議以base64
服用
usys() 給定加密串列及公鑰，返回encod過的串列。

原則上接受無上限長的公私鑰
