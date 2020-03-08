JIML
----
To understand infrastructure as code, we use Json Infrastructure markup language.The infrastructure consists of the following:

1)Load balancer (LB) <br>
2)Virtual machines (VMs) <br>
3)Database (DB) <br>
4)DNS entry <br>
5)Content delivery network (CDN) <br>
6)Bucket for static files 

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
			
The JIML tool creates a dependency graph from the json. The JIML tool turns the dependency graph into a linear flow of commands using pseudo language. The pseudo language represents the steps needed to create all the resources in the correct order. The nodes are easy to create because they have no dependencies, so they’re created first.

			$DB = database create {"password": "***", "engine": "MySQL"}

			$VM1 = virtualmachine create {"cpu": 2, "ram": 4, "os": "ubuntu"}
			$VM2 = virtualmachine create {"cpu": 2, "ram": 4, "os": "ubuntu"}

			$BUCKET = bucket create {}

			await [$DB, $VM1, $VM2]											--> wait for the dependencies
			$LB = loadbalancer create {"virtualmachines": [$VM1, $VM2]}

			await [$LB, $BUCKET]
			$CDN = cdn create {...}

			await $CDN
			$DNS = dns create {...}

			await $DNS
			
Using the command-line interface
--------------------------------
The AWS CLI is a convenient way to use AWS from your terminal. It runs on Linux, macOS, and Windows and is written in Python. It provides a unified interface for all AWS services. Unless otherwise specified, the output is by default in JSON format.

With CLI, you can create a deployment pipeline in a script file and execute that from CLI. A script or a blueprint can be reused, and will save you time in the long run.

You can install CLI, by downloading it from https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html

Configuring the CLI
-------------------
It’s strongly recommended that you not use the AWS root account and create a new user account from aws console. 

For creating new user, go to aws console --> IAM service --> Add user.

			Enter {yourusername} as the user name.
			Under Access Type, select Programmatic Access.
			Click the Next: Permissions button.
			
			Permissions tab
			---------------
			Select Attach existing policies directly
			Select Administrator Access policy to grant full permissions
			Click on review
			
When an user created in the complete tab, It gives access key id and secret key access. Copy these two values in a notepad. It is important to save secret key access, because there is no way to retrieve them from aws after initial setup. If you forget, you can create new secret key access.

To connect to aws cli, try the aws configure command. It asks for access key id, secret access key, region name, output format etc. Please find below the steps.

			aws configure
			AWS Access Key ID [None]: AKIAIRUR3YLPOSVD7ZCA
			AWS Secret Access Key [None]: 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
			Default region name [None]: us-east-1
			Default output format [None]: json
		
Now the CLI is configured. You can try various commands now.

Using CLI
---------
To use the AWS CLI, you need to specify a service and an action.

			aws <service> <action> [--key value ...]

One important feature of the CLI is the help keyword. You can get help at three levels of detail:

			aws help—Shows all available services.
			aws <service> help—Shows all actions available for a certain service.
			aws <service> <action> help—Shows all options available for the particular service action.

To get list of all regions
--------------------------
aws ec2 describe-regions

			Sample output:

			{
				"Regions": [
					{
						"Endpoint": "ec2.eu-north-1.amazonaws.com",
						"RegionName": "eu-north-1",
						"OptInStatus": "opt-in-not-required"
					},
					{
						"Endpoint": "ec2.ap-south-1.amazonaws.com",
						"RegionName": "ap-south-1",
						"OptInStatus": "opt-in-not-required"
					},
					{
						"Endpoint": "ec2.eu-west-3.amazonaws.com",
						"RegionName": "eu-west-3",
						"OptInStatus": "opt-in-not-required"
					},
					...
				]
			}

Applying filters
----------------
Suppose you want to get list of t2.micro instances which are already configured by you, The command is 

			aws ec2 describe-instances --filters "Name=instance-type,Values=t2.micro"

Sample output for my account
----------------------------
			{
				"Reservations": [
					{
						"Groups": [],
						"Instances": [
							{
								"AmiLaunchIndex": 0,
								"ImageId": "ami-08bc77a2c7eb2b1da",
								"InstanceId": "i-0f998c3a7ec64fa63",
								"InstanceType": "t2.micro",
								"KeyName": "mykey",
								"LaunchTime": "2020-03-07T15:22:33+00:00",
								"Monitoring": {
									"State": "disabled"
								},
								"Placement": {
									"AvailabilityZone": "us-east-1b",
									"GroupName": "",
									"Tenancy": "default"
								},
								"PrivateDnsName": "ip-172-31-80-133.ec2.internal",
								"PrivateIpAddress": "172.31.80.133",
								"ProductCodes": [],
								"PublicDnsName": "",
								"State": {
									"Code": 80,
									"Name": "stopped"
								},
								"StateTransitionReason": "User initiated (2020-03-07 15:28:21 GMT)",
								"SubnetId": "subnet-e22bc7c3",
								"VpcId": "vpc-10d6e46a",
								"Architecture": "x86_64",
								"BlockDeviceMappings": [
									{
										"DeviceName": "/dev/sda1",
										"Ebs": {
											"AttachTime": "2020-03-04T16:16:30+00:00",
											"DeleteOnTermination": true,
											"Status": "attached",
											"VolumeId": "vol-01d463c5760da61c7"
										}
									}
								],
								"ClientToken": "",
								"EbsOptimized": false,
								"EnaSupport": true,
								"Hypervisor": "xen",
								"NetworkInterfaces": [
									{
										"Attachment": {
											"AttachTime": "2020-03-04T16:16:29+00:00",
											"AttachmentId": "eni-attach-07d8fb46f9c273668",
											"DeleteOnTermination": true,
											"DeviceIndex": 0,
											"Status": "attached"
										},
										"Description": "",
										"Groups": [
											{
												"GroupName": "ssh-only",
												"GroupId": "sg-0893b94c2ab4f03a0"
											}
										],
										"Ipv6Addresses": [],
										"MacAddress": "12:5d:28:6d:79:57",
										"NetworkInterfaceId": "eni-0e420c84c6b6ac132",
										"OwnerId": "706207051201",
										"PrivateDnsName": "ip-172-31-80-133.ec2.internal",
										"PrivateIpAddress": "172.31.80.133",
										"PrivateIpAddresses": [
											{
												"Primary": true,
												"PrivateDnsName": "ip-172-31-80-133.ec2.internal",
												"PrivateIpAddress": "172.31.80.133"
											}
										],
										"SourceDestCheck": true,
										"Status": "in-use",
										"SubnetId": "subnet-e22bc7c3",
										"VpcId": "vpc-10d6e46a",
										"InterfaceType": "interface"
									}
								],
								"RootDeviceName": "/dev/sda1",
								"RootDeviceType": "ebs",
								"SecurityGroups": [
									{
										"GroupName": "ssh-only",
										"GroupId": "sg-0893b94c2ab4f03a0"
									}
								],
								"SourceDestCheck": true,
								"StateReason": {
									"Code": "Client.UserInitiatedShutdown",
									"Message": "Client.UserInitiatedShutdown: User initiated shutdown"
								},
								"Tags": [
									{
										"Key": "Name",
										"Value": "mymachine"
									}
								],
								"VirtualizationType": "hvm",
								"CpuOptions": {
									"CoreCount": 1,
									"ThreadsPerCore": 1
								},
								"CapacityReservationSpecification": {
									"CapacityReservationPreference": "open"
								},
								"HibernationOptions": {
									"Configured": false
								},
								"MetadataOptions": {
									"State": "applied",
									"HttpTokens": "optional",
									"HttpPutResponseHopLimit": 1,
									"HttpEndpoint": "enabled"
								}
							}
						],
						"OwnerId": "706207051201",
						"ReservationId": "r-01c640aac4fbe95ad"
					}
				]
			}

Extract data from Result
------------------------
The --query option uses JMESPath, which is a query language for JSON, to extract data from the result. This can be useful because usually you only need a specific field from
the result. The following CLI command gets a list of all AMIs in JSON format.

			aws ec2 describe-images

			Sample output
			
			{
			  "Images": [
				{
				  "ImageId": "ami-146e2a7c",
				  "State": "available"
				},
				{
				  "ImageId": "ami-b66ed3de",
				  "State": "available"
				}
				...
			  ]
			}

To start an EC2 instance, you need the ImageId without all the other information. To extract, the first ImageId property, the path is Images[0].ImageId; the result of this query is "ami-146e2a7c".

To extract all State properties, the path is Images[*].State; the result of this query is ["available", "available"].

			$ aws ec2 describe-images --query "Images[0].ImageId"
			"ami-146e2a7c"
			$ aws ec2 describe-images --query "Images[0].ImageId" --output text
			ami-146e2a7c
			$ aws ec2 describe-images --query "Images[*].State"
			["available", "available"]
