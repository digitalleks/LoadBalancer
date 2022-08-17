# LoadBalancer
Configuring Apache as a Loadbalancer

A new EC2 instance running Ubuntu Server 20.04 was created for this Load Balancer.  Below is the snapshot of the available instance showing the Webservers, the NFS Server and the Database server:\
<img width="737" alt="EC2 STATUS" src="https://user-images.githubusercontent.com/61512079/185150701-ee054959-8a40-46cf-81f3-f2650496689b.PNG">\

Port 80 was opened on the EC2 Instance security group as shown below:\
<img width="833" alt="Security group - http" src="https://user-images.githubusercontent.com/61512079/185152965-1e5f34da-7c09-4551-aca7-60a528d1a598.PNG">

Next is the installation of Apache Load Balancer.
```bash
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev
```
<img width="841" alt="apache-install" src="https://user-images.githubusercontent.com/61512079/185154010-ee835131-d333-4793-8913-aadc1faa0b6a.PNG">\
<img width="943" alt="apache-lb-install" src="https://user-images.githubusercontent.com/61512079/185154369-c4c6b4e9-2a23-4142-b5c8-15157f28980c.PNG">\

After the installation, the following commands were run to enable some modules:
```bash
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_byrequests
```
<img width="403" alt="modules-enabled" src="https://user-images.githubusercontent.com/61512079/185154946-1161f1a1-9962-44f7-aea8-d52b7653b460.PNG">\

Apache2 service was restarted and confirmed running:
```bash
sudo systemctl restart apache2
sudo systemctl status apache2
```
<img width="547" alt="apache2-status" src="https://user-images.githubusercontent.com/61512079/185155588-c193bf84-7e71-48c2-8105-422fca2657b4.PNG">

Next is the Configuration of the load balancer:
```bash
sudo vi /etc/apache2/sites-available/000-default.conf
```
The config entries is edited as shown below:
```vim
<Proxy "balancer://mycluster">
               BalancerMember http://172.31.33.120:80 loadfactor=5 timeout=1
               BalancerMember http://172.31.45.154:80 loadfactor=5 timeout=1
               ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
 ```
Then the apache2 service is restarted:
```bash
sudo systemctl restart apache2
```
<img width="547" alt="apache2-restart" src="https://user-images.githubusercontent.com/61512079/185158746-e89564df-c180-4e65-b57e-47116d4d9dac.PNG">

Before testing, we needed to be able to log each webserver access in its own log, so we unmounted the log directory from the NFS as confirmed below:
```bash
sudo umount  172.31.43.227:/mnt/logs /var/log/httpd
```
<img width="400" alt="umount-web1" src="https://user-images.githubusercontent.com/61512079/185160602-8eb1ce2a-362b-460e-bf3f-a46195c9e119.PNG">\
<img width="395" alt="umount-web3" src="https://user-images.githubusercontent.com/61512079/185161710-0372f45d-2503-470e-af36-371c0daa6ac6.PNG">

To test our load balancer, we open the public IP from the web browser multiple times as follow:
```bash
http://ec2-3-137-204-64.us-east-2.compute.amazonaws.com/index.php
```
Test page opened shown below:\
<img width="663" alt="LB-Test-Page" src="https://user-images.githubusercontent.com/61512079/185186404-2ff3611f-6593-45ef-8b2b-dfa2ded9d084.PNG">

Access logs for the two web servers are shown below: \
<img width="471" alt="Access_log_web1" src="https://user-images.githubusercontent.com/61512079/185186462-a2b541da-ee22-4fb8-84eb-cb4af9e0991d.PNG">\
<img width="450" alt="Access_log_web2" src="https://user-images.githubusercontent.com/61512079/185186527-cca14507-5f3c-4ccb-8082-e23dab164bdf.PNG">







