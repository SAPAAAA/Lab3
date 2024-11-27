![Block the PC1 accessing web server through SSH](https://github.com/user-attachments/assets/320edf83-d6a3-4279-8fa8-1268a3b37b09)# Bùi Thọ Anh Tú - 22110088

# Task 1: Firewall configuration 
**Question 1**: 
Setup a set of vms/containers in a network configuration of 2 subnets (1,2) with a router forwarding traffic between them. Relevant services are also required:
- The router is initially can not route traffic between subnets
- PC0 on subnet 1 serves as a web server on subnet 1
- PC1,PC2 on subnet 2 acts as client workstations on subnet 2 
**Answer 1**:

I will be using Docker to setup the lab environment.
The following docker-compose file is provided for this lab.

```yaml
version: '3.9'
services:
  router:
    image: alpine
    container_name: lab3-router
    command: sh -c "
      apk add --no-cache iptables iproute2 &&
      sysctl net.ipv4.ip_forward=0 &&
      sleep infinity"
    cap_add:
      - NET_ADMIN
    privileged: true
    networks:
      subnet1:
        ipv4_address: 10.0.1.1
      subnet2:
        ipv4_address: 10.0.2.1

  pc0:
    image: httpd
    container_name: lab3-pc0
    networks:
      subnet1:
        ipv4_address: 10.0.1.2
    volumes:
      - ./html:/usr/local/apache2/htdocs/
    ports:
      - "8080:80"

  pc1:
    image: alpine
    container_name: lab3-pc1
    command: sh -c "apk add --no-cache openssh-client iputils && sleep infinity"
    cap_add:
      - NET_ADMIN
    privileged: true
    networks:
      subnet2:
        ipv4_address: 10.0.2.2

  pc2:
    image: alpine
    container_name: lab3-pc2
    command: sh -c "apk add --no-cache openssh-client iputils && sleep infinity"
    cap_add:
      - NET_ADMIN
    privileged: true
    networks:
      subnet2:
        ipv4_address: 10.0.2.3

networks:
  subnet1:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 10.0.1.0/24
          gateway: 10.0.1.254
  subnet2:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 10.0.2.0/24
          gateway: 10.0.2.254
```

After creating the docker-compose file, run the following command to start the lab environment.

```bash
docker-compose up -d
```

Verify that the lab environment is running.

```bash
docker-compose ps
```

![Verify DOcker](https://github.com/user-attachments/assets/bbcd00da-7e78-4237-88e0-9282dc59ab89)

**Question 2**:
- Enable packet forwarding on the router.

Execute the router container.

```bash
docker exec -it lab3-router /bin/sh
```

Enable packet forwarding on the router.


```bash
sysctl net.ipv4.ip_forward=1
```

![Execute router and enable packet forwarding](https://github.com/user-attachments/assets/49a0c637-6a4a-41e6-851e-e5d5159b5e60)

**Question 3**:
  Config the router to block ssh to web server from PC1, leaving ssh/web access normally for all other hosts from subnet 1.

Check the iptables rules on the router.

```bash
iptables -L
```

![Check Iptables 1](https://github.com/user-attachments/assets/d456c7ef-c149-4ff3-8e66-52a5acef9bfa)

Config the router to block ssh to web server from PC1 (ip4 = 10.0.2.2)

```bash
iptables -A FORWARD -s 10.0.2.2 -d .1.2 -p tcp --dport 22 -j DROP
```

Check the iptables rules on the router.

```bash
iptables -L
```

![Block the PC1 accessing web server through SSH](https://github.com/user-attachments/assets/86f914a3-63e7-40ad-a949-37eb42ad9e7c)


**Question 4**:
- PC1 now servers as a UDP server, make sure that it can reply UDP ping from other hosts on both subnets.

```bash
ping 10.0.2.2
```

- Config personal firewall on PC1 to block UDP accesses from PC2 while leaving UDP access from the server intact.
**Answer 4**:


# Task 2: Encrypting large message 
Use PC0 and PC2 for this lab 
Create a text file at least 56 bytes on PC2 this file will be sent encrypted to PC0



**Question 1**:
Encrypt the file with aes-cipher in CTR and OFB modes. How do you evaluate both cipher in terms of error propagation and adjacent plaintext blocks are concerned. 
**Answer 1**:
- Demonstrate your ability to send file to PC0 to with message authentication measure.
- Verify the received file for each cipher modes
**Question 2**:
- Assume the 6th bit in the ciphered file is corrupted.
- Verify the received files for each cipher mode on PC0

**Answer 2**:

**Question 3**:
- Decrypt corrupted files on PC0.
- Comment on both ciphers in terms of error propagation and adjacent plaintext blocks criteria. 





