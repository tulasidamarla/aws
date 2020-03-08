
JIML
----
To understand infrastructure as code, we use Json Infrastructure markup language.The infrastructure consists of the following:

Load balancer (LB) <br>
Virtual machines (VMs) <br>
Database (DB) <br>
DNS entry <br>
Content delivery network (CDN) <br>
Bucket for static files 

Here is an example:

			{
			  "region": "us-east-1",				--> describes region
			  "resources": [
				{
				  "type": "loadbalancer",			--> A loadbalancer is needed
				  "id": "LB",
				  "config": {
					"virtualmachines": 2,			--> Need to vms
					"virtualmachine": {
					  "cpu": 2,
					  "ram": 4,
					  "os": "ubuntu"				--> ubuntu os
					}
				  },
				  "waitFor": "$DB"					--> LB can only be created if the DB is ready
				},
				{
				  "type": "cdn",					--> A CDN is used that caches requests to the LB or fetches static assets from a bucket
				  "id": "CDN",
				  "config": {
					"defaultSource": "$LB",
					"sources": [
					  {
						"path": "/static/*",
						"source": "$BUCKET"
					  }
					]
				  }
				},
				{
				  "type": "database",				--> Data is stored within a mysql db
				  "id": "DB",
				  "config": {
					"password": "***",
					"engine": "MySQL"
				  }
				},
				{
				  "type": "dns",					--> A dns entry points to CDN
				  "config": {
					"from": "www.mydomain.com",
					"to": "$CDN"
				  }
				},
				{
				  "type": "bucket",					--> bucket is used to store static assets
				  "id": "BUCKET"
				}
			  ]
			}
			
