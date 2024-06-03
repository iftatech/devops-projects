**Basic**

SayHi is a complete social platform with fully loaded modules developed using Yii2, Node.js and and Flutter Framework.
It comprises of Mobile Applications, Backend Penal. As This is complete social system with lots of modules for web and mobile apps, it is compulsory to have some basic knowledge in both server side and mobile app development if you want to do the installation, setup and publishing with your branding yourself.


1.Flutter SDK and JDK with path setup in your IDE.

2.Real server related knowledge like apache or local machine server,nodejs we preferred to use a real server.

3.Server related knowledge and we preferred cPanel in your server for quick installation

4.Basic knowledge in PHP, Yii2, Nodejs and Flutter if you want to do some customization yourself (Not compulsory).

5.basic knowledge about google cloud and firebase.

**Server**
Requires 

- PHP v8.1 and MySQL.

- Mod_rewrite Apache

- Ctype PHP, JSON, PDO, XML, Zip, Gd Extension

- Nodejs v18.16.0
 
- SSL Required

- Preferred Server: DigitalOcean, AWS


To set up a PHP 8.1 FPM site with Nginx on Ubuntu for the domain http://muslimgrams.com/, with the index.php file located at 
```yaml
/var/www/html/sayhi_v1.3_code/backend/web/index.php,
  ```
follow these steps:

**Step 1:** Install Required Software
First, make sure your system is up-to-date and install Nginx and PHP 8.1 FPM.

```yaml
sudo apt update
sudo apt upgrade -y
sudo apt install nginx php8.1-fpm php8.1-mysql -y
 ```

**Step 2:** Configure PHP-FPM
Ensure PHP-FPM is properly configured and running.

```yaml
sudo systemctl start php8.1-fpm
sudo systemctl enable php8.1-fpm
 ```

**Step 3:** Configure Nginx
Create a new Nginx configuration file for your site.

```yaml
sudo nano /etc/nginx/sites-available/muslimgrams.com
 ```
Add the following configuration to the file:

nginx
```yaml
server {
    listen 80;
    server_name muslimgrams.com www.muslimgrams.com;

    root /var/www/html/sayhi_v1.3_code/backend/web;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }

    access_log /var/log/nginx/muslimgrams_access.log;
    error_log /var/log/nginx/muslimgrams_error.log;
}
 ```

**Step 4:** Enable the Site
Enable the Nginx site configuration by creating a symbolic link from sites-available to sites-enabled.

```yaml
sudo ln -s /etc/nginx/sites-available/muslimgrams.com /etc/nginx/sites-enabled/
```

Remove the default configuration to avoid conflicts.

```yaml
sudo rm /etc/nginx/sites-enabled/default
```

**Step 5:** Test and Reload Nginx
Test the Nginx configuration for syntax errors.

```yaml
sudo nginx -t
```
If the test is successful, reload Nginx to apply the changes.

```yaml
sudo systemctl reload nginx
```
**Step 6:** Set Up Permissions
Ensure the correct permissions and ownership for your web directory.

```yaml
sudo chown -R www-data:www-data /var/www/html/sayhi_v1.3_code/backend/web
sudo chmod -R 755 /var/www/html/sayhi_v1.3_code/backend/web
```
**Step 7:** Configure DNS

Make sure your domain muslimgrams.com points to your server's IP address. This is typically done via your domain registrar's DNS settings.

**Step 8:** Verify the Setup
Visit http://muslimgrams.com in your web browser. If everything is configured correctly, you should see your PHP application running.

Optional: Enable SSL with Let's Encrypt
For a secure HTTPS setup, you can use Let's Encrypt. First, install Certbot.

```yaml
sudo apt install certbot python3-certbot-nginx -y
```
Then, obtain and install an SSL certificate.

```yaml
sudo certbot --nginx -d muslimgrams.com -d www.muslimgrams.com
```
Follow the prompts to configure SSL. Certbot will automatically adjust your Nginx configuration to use the new certificate.

**To ensure database connectivity for your application, follow these steps to make sure MySQL is running and your application can connect to it:**

Ensure MySQL is Running:

Check the status of the MySQL service:

```yaml
sudo systemctl status mysql
```
If MySQL is not running, start the service:

```yaml
sudo systemctl start mysql
```
Enable MySQL to start on boot:

```yaml
sudo systemctl enable mysql
```
**Secure MySQL Installation:**

Run the MySQL secure installation script to set up root password, remove anonymous users, disallow remote root login, and remove test databases:

```yaml
sudo mysql_secure_installation
```
**Create a Database and User:**

Log in to MySQL as the root user:
sh
```yaml
sudo mysql -u root -p
```
**Create a new database:**
sql
```yaml
CREATE DATABASE your_database_name;
```
**Create a new user and grant them permissions on the database:**
sql
```yaml
CREATE USER 'your_username'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON your_database_name.* TO 'your_username'@'localhost';
FLUSH PRIVILEGES;
```
**Test MySQL Connection:**

Ensure you can connect to the database using the new user credentials:
sh
```yaml
mysql -u your_username -p your_database_name
```
**Configure PHP to Connect to MySQL:**

Ensure the pdo_mysql extension is enabled, as described in the previous steps.
**Create a PHP script to test the database connection. Save it as test_db.php in your web root directory (/var/www/html/):**
php
```yaml
<?php
$dsn = 'mysql:host=localhost;dbname=your_database_name';
$username = 'your_username';
$password = 'your_password';

try {
    $dbh = new PDO($dsn, $username, $password);
    echo "Connected to database successfully!";
} catch (PDOException $e) {
    echo "Connection failed: " . $e->getMessage();
}
?>
```
**Access the Test Script in a Browser:**

Open your web browser and navigate to http://your_server_ip/test_db.php. You should see a message saying "Connected to database successfully!" if the connection is successful.
**Troubleshooting:**

If you encounter any issues, check the following:
Ensure MySQL is running and the credentials are correct.
Verify that the pdo_mysql extension is enabled and correctly configured.
Check MySQL logs for any errors:
sh
```yaml
sudo tail -f /var/log/mysql/error.log
```
**Check Apache error logs for any errors related to PHP:**
sh
```yaml
sudo tail -f /var/log/apache2/error.log
```
By following these steps, you can ensure that MySQL is running and your PHP application can connect to the database.

**To check if a specific SQL file, such as the one located in doc/db/.sql, has been imported into your MySQL database named database_sayhi, you can follow these steps:**

**Step 1:** Identify Database Structure
First, understand the structure of the database file you are checking. A typical .sql file includes table creation statements and possibly data insertion statements. Knowing the structure helps in verifying if the tables and data exist in your database.

**Step 2:** Log into MySQL
Log into your MySQL server using the MySQL command line client:

sh
```yaml
mysql -u your_username -p
```
Replace your_username with your MySQL username. Enter your password when prompted.

**Step 3:** Select the Database
Select the database where you want to check for the imported tables and data:

sql
```yaml
USE database_sayhi;
```
**Step 4:** Check for Tables
List the tables in the database to verify if the expected tables from the .sql file are present:

sql
```yaml
SHOW TABLES;
```
Review the output to see if the tables that should have been created by the .sql file are listed.

**Step 5:** Check for Data
You can also check if the expected data exists in these tables. For example, if the .sql file includes inserting data into a table named users, you can run a query like:

sql
```yaml
SELECT * FROM users LIMIT 10;
```
This will display the first 10 rows in the users table, helping you verify if the data was imported correctly.

**Db configuration can be updated in common/config/main-local.php.**
Update dbname, username and password.

```yaml
<?php
              return [
                  'components' => [
                      'db' => [
                          'class' => 'yii\db\Connection',
                          'dsn' => 'mysql:host=localhost;dbname=DB_NAME####',
                          'username' => '#####',
                          'password' => '######',
                          'charset' => 'utf8',
                      ],
                      'mailer' => [
                          'class' => 'yii\swiftmailer\Mailer',
                          'viewPath' => '@common/mail',
                          'transport' => [
                              'class' => 'Swift_SmtpTransport',
                              'host' => 'smtp-relay.sendinblue.com',  // e.g. smtp.mandrillapp.com or smtp.gmail.com
                              'username' => '############',
                              'password' => '###########',
                              'port' => '587', // Port 25 is a very common port too
                              'encryption' => 'tls', // It is often used, check your provider or mail server specs
                          ],
                      ],
                      


                  ],
              ];

```


**Site Url and Envato purchase code**
Site url can be update from common/config/params.php

 ```yaml
<?php

              'adminEmail' => 'admin@yourdomain.com',
              'supportEmail' => 'support@yourdomain.com',
              'senderEmail' => 'admin@yourdomain.com',
              'senderName' => 'SayHi',
              
              'siteMode' => 1, // 1 for live, 2 for testing , 3 demo
              'siteUrl' => 'https://example.com',// domain here
              'enventoPurchaseCode' => '##########', // envato purchase code
              'releaseVersion' => '1.3', // current installed product version
              'storageSystem'=> 1,// storage system (local storage =1, AWS S3=2, AZURE=3)
              's3' => [
                  'key' => '##############',
                  'secret' => '##############',
                  'region' => '##############',
                  'defaultBucket' => '#########', //name
                  'storageUrl'=>'https://[bucket_name].s3.amazonaws.com'  //aws s3 storage url /cdn url
                  
                  
              ],
```

**All done for appache srever, your software is ready to run.**
            
**Admin Url:** youdomin.com/backend/web/index.php

**Login information :**

Username : admin

Password : 123456

**Nodejs Chat Socket Setup**

We are using nodeJs socket programming for chat, Use following instruction for chat socket setup

All nodeJs chat code will be in /chat forder

**STEP 1**

Nodejs v18.16.0 

must be installed on your server
To confirm you have nodejs install, try to run command in your console :

```yaml
node -v
```
You must see installed version of nodejs
Output
v18.16.0
**It the nodejs is already not install you can install Nodejs with few steups.**

Open the ssh terminal and run following commands:

To get this version, you can use the apt package manager. Refresh your local package index first:

```yaml
sudo apt update
Then install Node.js:
sudo apt install nodejs
```
Check that the install was successful by querying node for its version number:

node -v
Output
v18.16.0

**Install the npm package with apt for installing other moduels like pm2:**

```yaml
sudo apt install npm
```

**You can update configuration filechat/config.json with given fields**

```yaml
{
  "port":4000,  //server port
    "live":{    // add your db information
      "host": "127.0.0.1",    
      "user": "######",
      "password": "######",
      "database": "#######"
    },
    "dev":{ // leave it blank
      "host": "####",
      "user": "#####",
      "password": "####",
      "database": "###"
    }
  },
  "encryptionKey":"##########################", // enter key for encryption chat eg. bbC2H19lkVsQDf74rtNMQdddFloLyws
  "storageUrl":"https://[bucket_name].s3.amazonaws.com   OR  https://yoursite.com/uploads",  //Use bucket url for AWS S3 OR use https://yoursite.com/upload for local storage   Check For S3
  "pushNotification":{    
      "databaseURL": "https://q#############.firebaseio.com"         See here
  },
  "voipNotification":{                                               Check documentation to create
    "key": "cert/AuthKey_#####.p8", // .p8 file
    "keyId": "########", //67J4MZG469
    "teamId": "########", // T937GPNTUY
    "bundleId":"###########"  //com.example.app
  },
  "sslCertificatePath":{
    "isSsl":true,   //if ssl not available then make fasle
    "key": "############/##privkey.pem",     // ssl key certificate path
    "cert": "##############/##fullchain.pem" // ssl certificate path
    
  }

```

**Start node server**

After installing all dependencies and updated the configuration, you must start node server :
Open a terminal and nvigate to the /chat folder that contain the file "index.js"
Run following command to start server :

```yaml
          cd /path/to/your/root forlder/chat
          node index.js
        ```

        
**OR**
For running server all time you can install pm2 package More Detail
Run following command to run server with forever:
```yaml
          cd /path/to/your/root forlder/chat
          pm2 start index.js
        
```
        
Visit **https://yourdomain:4000** in your browser. You will see following screen if server is started successfully.

**Mandatory setup**

**Mail Configuration (SMTP)**

Mail Configurations part admin can set his Mailer host, user name and his own encryption method and password for this SMTP Mail setup. This configuratin is used for sending mails.

MAil configuration can be updated in common/config/main-local.php.

```yaml
<?php
        return [
            'components' => [
                'db' => [
                    'class' => 'yii\db\Connection',
                    'dsn' => 'mysql:host=localhost;dbname=DB_NAME####',
                    'username' => '#####',
                    'password' => '######',
                    'charset' => 'utf8',
                ],
                'mailer' => [
                    'class' => 'yii\swiftmailer\Mailer',
                    'viewPath' => '@common/mail',
                    'transport' => [
                        'class' => 'Swift_SmtpTransport',
                        'host' => 'smtp-relay.sendinblue.com',  // e.g. smtp.mandrillapp.com or smtp.gmail.com
                        'username' => '############',
                        'password' => '###########',
                        'port' => '587', // Port 25 is a very common port too
                        'encryption' => 'tls', // It is often used, check your provider or mail server specs
                    ],
                ],
                


            ],
        ];

```

**Local server storage setting**

Storage system can update be update from common/config/params.php
set storageSystem=1 for local storage

```yaml
<?php

        'adminEmail' => 'admin@yourdomain.com',
        'supportEmail' => 'support@yourdomain.com',
        'senderEmail' => 'admin@yourdomain.com',
        'senderName' => 'SayHi',
        
        'siteMode' => 1, // 1 for live, 2 for testing , 3 demo
        'siteUrl' => 'https://example.com',// domain here
        'enventoPurchaseCode' => '##########', // envato purchase code
        'releaseVersion' => '1.3', // current installed product version
        'storageSystem'=> 1,//  storage system ( local storage =1, AWS S3=2, AZURE=3)
        's3' => [
            'key' => '##############',
            'secret' => '##############',
            'region' => '##############',
            'defaultBucket' => '#########', //name
            'storageUrl'=>'https://[bucket_name].s3.amazonaws.com'  //aws s3 storage url
            
        ],
        'azureFs' => [
            'accountName' => '###########',
            'accountKey' => '##########',
            'container' => '#######'
            
        ],
```
**Amazone S3 Setup**

We are using amazone s3 for storage files and images.

**STEP 1**

Create Bucket
You can create bucket on s3 with public access to follow this URL:
https://docs.aws.amazon.com/quickstarts/latest/s3backup/step-1-create-bucket.html .

**STEP 2**

Update AWS S3 Access key and secret access key

**Use following url to create secret keys:**

https://docs.aws.amazon.com/powershell/latest/userguide/pstools-appendix-sign-up.html#get-access-keys.

After generate the keys and secret, update it with bucket name on common/config/params.php
set storageSystem=2 for AWS s3 storage, and update s3 information as below

```yaml
<?php
    
            'adminEmail' => 'admin@yourdomain.com',
            'supportEmail' => 'support@yourdomain.com',
            'senderEmail' => 'admin@yourdomain.com',
            'senderName' => 'SayHi',
            
            'siteMode' => 1, // 1 for live, 2 for testing 
            'siteUrl' => 'https://example.com',// domain here
            'storageSystem'=> 2,  // storage system (local storage =1, AWS S3=2)
            's3' => [
                'key' => '##############',
                'secret' => '##############',
                'region' => '##############',
                'defaultBucket' => '##############', //bucket name
                'storageUrl'=>'https://[bucket_name].s3.amazonaws.com '  //aws s3 storage url
                
                
            ],
```

**Project access URL**

Admin Panel
Admin Url : youdomin.com/backend/web/index.php

**Login information :**

Username : admin
Password : 123456

**Socket Url (socketApiBaseUrl)**

https://yourdomain:4000

**Rest API Endpoint Url (restApiBaseUrl)**

youdomin.com/api/web/v1/

API name will be added in the last of above api endpoint url. This will be done on the mobile side by programming. suppose if we need to call categories api, then mobile application is calling following url with adding api name "categories" like :

youdomin.com/api/web/v1/categories

if the above url is working then Rest api setup successfully.
    
          
  


      
   

