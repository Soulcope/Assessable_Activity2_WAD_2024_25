# Assessable_Activity2_WAD_2024_25

# EXPLANATION EC2

## 1. Create an EC2 Instance on AWS

To begin with, we need to create a new instance with the latest Ubuntu Server AMI, t2.micro instance type, vockey key, and a security group that allows port 80 and 22.

## 2. Create Elastic IP

We create an elastic IP and associate it with the instance.

## 3. Access Instance and Install Packages

We access the instance through SSH with the PEM key:

```sh
ssh -i labuser.pem ubuntu@ec2-3-93-90-184.compute-1.amazonaws.com
```

Once we’ve accessed the instance, we update and install the packages needed for the activity:

```sh
sudo apt-get update 
sudo apt-get install apache2 
sudo apt-get install npm
sudo npm install -g pm2 express
```

## 4. Clone and Deploy the React Project

We clone the repository and install dependencies:

```sh
sudo git clone https://github.com/gisgarme/adivina-el-numero.git
cd adivina-el-numero
```

After accessing the project, we need to add the following line to `package.json`:

```json
"homepage": "http://ec2-3-93-90-184.compute-1.amazonaws.com"
```

We can do this using nano:

```sh
sudo nano package.json
```

Then, we install the dependencies of the project:

```sh
sudo npm install
```

We run the project with:

```sh
sudo pm2 start npm --name "vite-server" -- run dev -- --host
```

## 5. Set Up Apache Proxy

We enable the proxy module:

```sh
sudo a2enmod proxy proxy_http
sudo service apache2 restart
```

Then, we edit the virtual host configuration:

```sh
sudo nano /etc/apache2/sites-available/000-default.conf
```

We need to add the DNS as `ServerName`, comment out `DirectoryRoot`, and add `ProxyPass` and `ProxyPassReverse` directives to redirect from port 80 to port 5173, the port that PM2 will use:

```apache
ServerName ec2-3-93-90-184.compute-1.amazonaws.com
ProxyPass / http://127.0.0.1:5173/
ProxyPassReverse / http://127.0.0.1:5173/
```

Then, we save and restart Apache:

```sh
sudo service apache2 restart
```

## 6. Check if It Works

Now we should check the URL to verify if the project is being deployed correctly.
