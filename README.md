# Hadoop
## Environment Setup
1. Install Java
```
sudo apt install default-jdk default-jre
```
2. Install OpenSSH server and client
```
sudo apt install openssh-server openssh-client
```
3. Add new user
```
sudo adduser hadoop
```
4. Switch to new user
```
sudo su - hadoop
```
5. Generate SSH public and private keys
```
ssh-keygen -t rsa
```
6. Add SSH public key to authorized keys
```
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
7. Change permissions of authorized keys
```
chmod 640 ~/.ssh/authorized_keys
```
8. Verify SSH configuration
```
ssh localhost
```
9. Get [latest version of Hadoop](https://downloads.apache.org/hadoop/common/stable/)
```
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
```
10. Extract tar file
```
tar -xvzf hadoop-3.3.6.tar.gz
```
11. Rename directory
```
mv hadoop-3.3.6 hadoop
```
12.  Obtain Java environment directory
```
dirname $(dirname $(readlink -f $(which java)))
```
13.  Set variables

## References
Kumar, R. (2022, October 28). [How to install and configure Hadoop on Ubuntu 20.04](https://tecadmin.net/install-hadoop-on-ubuntu-20-04/). Tec Admin.

Morubasi, F. (2022, April 21). [Installing Hadoop on Ubuntu 20.04](https://medium.com/@festusmorumbasi/installing-hadoop-on-ubuntu-20-04-4610b6e0391e). Medium.

Shamar, S. (2023, March 17). [Install Hadoop on Ubuntu](https://learnubuntu.com/install-hadoop/). Learn Ubuntu.

Tutorials Point (2014, December). [Hadoop Tutorial](https://www.tutorialspoint.com/hadoop/index.htm).
