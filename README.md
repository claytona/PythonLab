PythonLab
=========
// client
#!/bin/env python

import socket, struct
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('192.168.96.135', 1333))
m = 'hello world'
version = 1
type = 1
paylen = len(m)
data = struct.pack('!BBH', version, type, paylen)
print(s.send(data + m))

Rdata = ''
while len(Rdata) < 4:
    Rdata += s.recv(4)
version, type, paylen = struct.unpack('!BBH', Rdata)
print(version, type, paylen)
print(s.recv(paylen))

// server
#!/bin/env python

import socket, struct, json, random
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.setsockopt(socket.SOL_SOCKET,socket.SO_REUSEADDR,1)
s.bind(('' , 1333))
s.listen(5)
while 1:
    conn, addr = s.accept()
    data = conn.recv(4)
    version, type, paylen = struct.unpack('!BBH', data)
    m = conn.recv(paylen)
    print(version, type, paylen, m)
   
    if type == 1:
        response = m
    if type == 2:
        response = json.dumps(random.random())
     
    Rversion = 1
    Rtype = 0
    Rpaylen = len(response)
    Rdata = struct.pack('!BBH', Rversion, Rtype, Rpaylen)
    print(conn.send(Rdata + response))
    print(Rversion, Rtype, Rpaylen, response)
