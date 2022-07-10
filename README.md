# What is this?
This project implement simple username/password authentication like auth-user-pass of openvpn, which based on [simpletun](https://github.com/gregnietsky/simpletun)


# Prepare the environment
simpletun is a simple tunnel example, like VPN.
so you need a client and a server in the basic requirement.

### Server - Can be access by client in the internet. 
Reference to this artical, we can setup the basic environment for simpletun usage.
[https://www.anquanke.com/post/id/248136](https://www.anquanke.com/post/id/248136)

## Create tun0 interface for simpletun usage at client and server
In this example, both client and server are located in the same virtual network segment **192.168.100.0/24**
### Client:
```bash
# Use ip tuntap to create the interface 'tun0'
sudo ip tuntap add dev tun0 mod tun
# Assign a ip to tun0
sudo ifconfig tun0 192.168.100.10 netmask 255.255.255.0
# Checkout the setting of tun0
ifconfig tun0
```

### Server:
```bash
# Use ip tuntap to create the interface 'tun0'
sudo ip tuntap add dev tun0 mod tun
# Assign a ip to tun0
sudo ifconfig tun0 192.168.100.11 netmask 255.255.255.0
# Checkout the setting of tun0
ifconfig tun0
```

# Build simpletun and run
To compile the program, just do
```bash
gcc simpletun.c -o simpletun
```
If you have GNU make, you can also exploit implicit targets and do
```bash
make simpletun
```

-------

## Usage:
```text
simpletun -i <ifacename> [-s|-c <serverIP>] [-p <port>] [-w <password>] [-u|-a] [-d]

simpletun -h

-i <ifacename>: Name of interface to use (mandatory)
-s|-c <serverIP>: run in server mode (-s), or specify server address (-c <serverIP>) (mandatory)
-w <password>: password for access tun
-p <port>: port to listen on (if run in server mode) or to connect to (in client mode), default 55555
-u|-a: use TUN (-u, default) or TAP (-a)
-d: outputs debug information while running
-h: prints this help text
```
----
## Run:
Run the server side with password : **correct_password**

```bash
[server]$ sudo ./simpletun -i tun0 -s -w correct_password
```
Run the client side with password

```bash
[client]$ sudo ./simpletun -i tun0 -c 10.2.3.4 -w wrong_password
connect failed, wrong password.
[client]$ sudo ./simpletun -i tun0 -c 10.2.3.4 -w correct_password
connected, tunnel established.
```

where 10.2.3.4 is the remote server's IP address, and tun0 must be 
replaced with the names of the actual tun interfaces used on the computers.
By default it assumes a tun device is being used (use -u to be explicit), and
-a can be used to tell the program that the interface is tap. 
By default it uses TCP port 55555, but you can change that by using -p (the 
value you use must match on the client and the server, of course). Use -d to 
add some debug information. Press ctrl-c on either side to exit (the other end
will exit too).

The program is very limited, so expect to be disappointed.
