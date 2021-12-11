# CIDR and VPC Subnets

## Things to remember

iPV4 = 32 bit address space

## Theory

- Subnets
  ```
  2^#of subnet bits
  ```
- Valid Hosts
  ```
  2^#of host bits - 2
  ```

## Examples

### example#1
```
                             192.168.1.1   
IP:     192.168.1.0 / 24   { to
        --------- ^          192.168.1.254
        network   |
                  |
                  |
            number of Hosts

        192.168.1.0 is the network number of the subnet
                  --
        192.168.1.255 is the boradcast address
```

(iPV4 address spacae - ) = 

> 32 - 24 = 8
> 
> 2^8 = 256

### example#2
```
                               192.168.1.129   
IP:     192.168.1.128 / 25   { to
        --------- ^            192.168.1.254
        network   |
                  |
                  |
            number of Hosts

        192.168.1.128 is the name of the subnet
                  --
        192.168.1.255 is the boradcast IP
```

> 32 - 25 = 7
> 
> 2^7 = 128

### example#3
```
                               192.168.1.129   
IP:     192.168.1.128 / 26   { to
        --------- ^            192.168.1.191
        network   |
                  |
                  |
            number of Hosts

        192.168.1.128 is the name of the subnet
                  --
        192.168.1.192 is the boradcast IP
```

> 32 - 26 = 6
> 
> 2^6 = 64


## How to approach the problem?

> Is it a Class A, B or C network?

`Class A`: 

`Class B`: 

`Class C`: 


## Decisions

- The higher the number after the slash, the smaller the network become.
- The first IP address always represent the subnet name.
- The last IP address of the network always represent the broadcast.


## Info
- My public IP at 1005 AF B, Lund - IPv4: 46.162.77.240

## References
1 [The Five IPv4 Classes - Quick Reference](https://www.meridianoutpost.com/resources/articles/IP-classes.php)