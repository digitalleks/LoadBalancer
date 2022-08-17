# LoadBalancer
Configuring Apache as a Loadbalancer

A new EC2 instance running Ubuntu Server 20.04 was created for this Load Balancer.  Below is the snapshot of the available instance showing the Webservers, the NFS Server and the Database server:/\
<img width="737" alt="EC2 STATUS" src="https://user-images.githubusercontent.com/61512079/185150701-ee054959-8a40-46cf-81f3-f2650496689b.PNG">\

Port 80 was opened on the EC2 Instance security group as shown below:\
<img width="833" alt="Security group - http" src="https://user-images.githubusercontent.com/61512079/185152965-1e5f34da-7c09-4551-aca7-60a528d1a598.PNG">\

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
sudo a2enmod lbmethod_bytraffic
```
<img width="403" alt="modules-enabled" src="https://user-images.githubusercontent.com/61512079/185154946-1161f1a1-9962-44f7-aea8-d52b7653b460.PNG">\

Apache2 service was restarted and confirmed running:\
```bash
sudo systemctl restart apache2
sudo systemctl status apache2
```
<img width="547" alt="apache2-status" src="https://user-images.githubusercontent.com/61512079/185155588-c193bf84-7e71-48c2-8105-422fca2657b4.PNG">\

Next is the Configuration of the load balancer:
```bash
sudo vi /etc/apache2/sites-available/000-default.conf
```




