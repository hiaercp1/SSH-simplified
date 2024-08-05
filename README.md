# Get secure🛡️ and connected🔗!
---
## Intro
SSH, or Secure Shell, is a network protocol that allows you to securely access and manage remote servers and devices over an encrypted connection. It provides a secure way to communicate with your server, transfer files, and execute commands remotely. Learning how to use SSH can greatly increase your productivity and efficiency as a developer or system administrator. It eliminates the need to physically be in front of a server to perform tasks, and also ensures that your communications and data remain secure from prying eyes.
So, if you're ready to take your remote server management skills to the next level, buckle up and get ready to dive into the world of SSH!


## What are you going to learn? 🤓

- [Structure of the SSH protocol.](#structure-of-the-ssh-protocol)
- [How to install SSH.](#how-to-install-ssh)
- [Enabling SSH.](#enabling-ssh)
- [SSH with certificates.](#ssh-with-certificates)
- [️Hardering SSH.](#hardering-ssh)

## Structure of the **SSH** protocol:
### **🧬SSH packet structure🧬**
- #### _Package length_
size: 4 bytes
purpose: This specifies the total length of the packet, including the length of the packet length field itself. It is usually 32 bits in size.
- #### _Padding amount_
size: 1 byte
purpose: This field is used to pad the packet to a specified block size for encryption. It is usually 8 bits in size.
- #### _Payload_
size: depends
purpose: This contains the actual data being transmitted, such as a command or file transfer. The size of the payload can vary depending on the specific data being sent.
- #### _Padding_
purpose: This padding is added to the payload to ensure that the total length of the packet is a multiple of the encryption block size. It is typically filled with random data and is used to prevent   padding oracle attacks.
- #### _MAC (Message Authentication Code)_
size: depends on the encryption algorithm used
purpose: The MAC field contains a cryptographic hash that is used to verify the integrity of the packet. By checking the MAC, the recipient can ascertain that the packet has not been tampered with during transmission.

## How to install **SSH**:
Usually SSH is installed on most Linux distributions by default and you can enable and use it right away, but if you're not sure if this is you're case, you can always check if it's installed by running the following command to check the version of SSH.
```sh
ssh -V
```
If you are in the rare situation when you don't have SSH installed by default, you can run the following commands to install it.
### Debian based distributions:
```sh
sudo apt install openssh-client
```
```sh
sudo apt isntall openssh-server
```
### RHEL:
```sh
sudo dnf install openssh
```
```sh
sudo dnf install openssh-server
```
### Arch based distributions:
```sh
sudo pacman -S openssh
```
## Enabling **SSH**:
In order to enable SSH we need to execute some simple commands depending on the system you are using as a backend for your Linux distribution. You might be running on an older Unix System V system or the newer SystemD based system.

### System V:

```sh
sudo service sshd start
```
### SystemD:

```sh
sudo systemctl start sshd
```
Once you exeute the right command for your's Linux system, **_Congratulations!_** you just started a SSH service. This is all fine but there is one problem, it is not persistent, that means that if you reboot your computer it will be disabled. To make it persistent you need to execute one more command.

### System V:

```sh
sudo chkconfig sshd on
```

### SystemD:

```sh
sudo systemctl enable sshd
```

You can connect to the server by executing the following command:

```sh
ssh USERNAME@SERVER_IP_ADDRESS
```
Now SSH should be up and running. In the next section we will discover ways to make it more secure.

## **SSH** with certificates:

One of the ways to secure our SSH is by impoving the way we authenticate to the server. By now we were using a pair of _username_ and _password_, but passwords can be guessed, keylogged and brute-forced. In order to mitigate this risk we can use **certificates**.

First of all we need to generate two keys, _Public_ key and a _Private_ key.

- **Private key**
  This key as a name suggest must be kept private, you do not give this key to anyone. This key is used to decrypt data that were encrypted by the Public key.
- **Public key**
  This key can be shared and we want to put it the the servers we intend to connect to. You can even share it on the internet and it's totally safe. The purpose of a Public key is just to encrypt data.

We can do this by executing the following command:

Execute it on the client side **NOT** on the server side!
```sh
ssh-keygen -t rsa 
```
Now we need to tell the server to trust our machine based on the generated certificate.
You can navigate to the .ssh directory and you will see three files: **id_rsa**, **id_rsa.pub**, **known_hosts**.
```sh
cd .ssh
```
The **id_rsa** is the private key, and the **id_rsa.pub** is the public key.

Now there are two ways to get the public key loaded to the SSH server.

### ssh-copy-id command:

you can use the ssh-copy-id utility to copy the certificate to the desired server by executing this command:

```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub USERNAME@SERVER_IP_ADDRESS
```
### Copying the public certificate manualy:

If you do not want to use the ssh-copy-id utility you can always just do it manualy.
1. Copy the contents of the **id_rsa.pub** file.
2. Connect to the server.
```sh
ssh USERNAME@SERVER_IP_ADRESS
```
3. Navigate to the .ssh directory (if you don't have it, just manualy creaty it and cd into it.)
4. Execute the following command and paste the contents of the **id_rsa.pub** file there.

```sh
vim ./authorized_keys
```
After this setup we need to enable the Public key authentication and disable the password authentication.
We can do this by going to the **/etc/ssh/sshd_config** file and uncomment the line **PubkeyAuthentication yes**.
(you can uncomment a line by deleting the **#** symbol at the start of the line)
To disable the password authentication we can find the line that says **PasswordAuthentication yes** and change the yes to no, **PasswordAuthentication no**.

Restart the service by running `sudo systemctl restart sshd` and you should be good to go :)

## Hardering **SSH**:

Now to the latest section and that is **Hardering**. In this section we will talk about securing your SSH.

- First of all we should disable the SSH 1 protocol. SSH 1 protocol is old, insecure and weak so we want to use just the newest and greatest protocol which is the SSH 2.
  To do so go to the **/etc/ssh/sshd_config** find a line that says **Protocol X** where X is the protocol version and make sure you only allow the SSH 2.

- After only allowing the second version of the SSH protocol we might want to filter who is allowed to ssh in.
  Most of the Linux distributions will prohibit the _root_ user, you can check this by going to the **/etc/ssh/sshd_config** file and look at the line that says **PermitRootLogin no**. If you see **PermitRootLogin yes** you can comment this line to ban the _root_ user from sshing     in or change the yes to no. If you want to filter who can ssh in you can add a line in the **/etc/ssh/sshd_config** that  says **AllowUsers user1 user2** where user1 and user2 is the usernames you want to let in (you can list more than 2 or less than 2, It's completely up to you.)

