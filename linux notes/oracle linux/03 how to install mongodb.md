# Install Mongodb on Oracle Linux

Source: https://www.atlantic.net/dedicated-server-hosting/how-to-install-and-secure-mongodb-on-oracle-linux-8/


### 1. Add MongoDB Repository
The MongoDB package is not available in the Oracle 
Linux 8 default repo, so you will need to create the 
MongoDB repo on your system.

You can create it by running the following command:

```
$ sudo vim /etc/yum.repos.d/mongodb-org-4.4.repo
```

Add the following contents:

```
[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
```

Save and close the file once you are done.

### 2. Install MongoDB on Oracle Linux 8
After creating a MongoDB repo, you can install the MongoDB package by just 
running the following command:

```
$ dnf install mongodb-org -y
```
_-y_ key is used to download and install all necessary dependencies.

Once the MongoDB is installed, start and enable the MongoDB service with the following command:

```
$ systemctl start mongod
$ systemctl enable mongod
```
To verify the MongoDB version, run the following command:

```
mongod --version
```

You will get the following output (similar):

```
db version v4.4.14
Build Info: {
    "version": "4.4.14",
    "gitVersion": "0b0843af97c3ec9d2c0995152d96d2aad725aab7",
    "openSSLVersion": "OpenSSL 1.1.1k  FIPS 25 Mar 2021",
    "modules": [],
    "allocator": "tcmalloc",
    "environment": {
        "distmod": "rhel80",
        "distarch": "x86_64",
        "target_arch": "x86_64"
    }
}
```

### 3. Configure MongoDB
By default, MongoDB can be connected without any authentication. For security purposes, it is recommended to enable MongoDB authentication.

First, edit the MongoDB configuration file and enable authentication:

```
$ sudo vim /etc/mongod.conf
```

Add the following lines:

```
security:
 authorization: enabled
```

__Save and close__ the file when you are finished.

### 4. reate an Admin User for MongoDB
Next, you will need to create an admin user and set a password to manage the MongoDB databases.

First, connect to the MongoDB instance with the following command:

```
mongo
```

Once you are connected, create a database named admin using the following command:

```
use admin
```

Next, create an admin user and set a password with the following command:

```
db.createUser(
{
user: "mongoadmin",
pwd: passwordPrompt(),
roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
}
)
```

You will be asked to set a password as shown below:
```
Enter password: 
```

Then you will see:
```
Successfully added user: {
	"user" : "mongoadmin",
	"roles" : [
		{
			"role" : "userAdminAnyDatabase",
			"db" : "admin"
		},
		"readWriteAnyDatabase"
	]
}
```

Next, exit from the MongoDB shell with the following command:
```
exit
```

Finally, restart the MongoDB service to apply the changes:
```
systemctl restart mongod
```

You can now connect to the MongoDB instance using the administrator credentials:
```
mongo --port 27017 --authenticationDatabase "admin" -u "mongoadmin" -p
```

Then you will be asked for the password. Enter the password.

Once you are logged in, you should see the following output (similar):
```
MongoDB shell version v4.4.14
Enter password: 
connecting to: mongodb://127.0.0.1:27017/?authSource=admin&compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("1d5be9b1-d0bd-4b6a-9ca7-adecddf5f9dd") }
MongoDB server version: 4.4.14
```

### 5. Create a Database in MongoDB
In this section, we will show you how to interact with the MongoDB database.

To create a database named wpdb, run the following command:
```
use wpdb
```

Next, add some data to the wpdb and _person_ collection using the following command:
```
db.person.insertOne(
  { "Hitesh Jethva" : "41",
   "Vyom Jethva" : "8",
   "Raj Shah" : "18"
  }
)
```

You can now list available databases using the following command:
```
db
```

To show documents in your database, run the following command:
```
show collections
```

To show the contents of your database collection, run the following command:
```
db.person.find()
```

