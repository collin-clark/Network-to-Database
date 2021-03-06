Changing this to run purely in Linux (not WSL) and on a single host. May dockerize it later.

--------------------------------------------------------------------------------------------------
Demo: https://www.youtube.com/watch?v=koJc_LmFpAI&feature=youtu.be

This program will run though your Cisco gear, run a bunch of commands on the IP addresses in IPs.txt, and put data from those commands into a database.  It will also find other devices in your network to pull data from and stick into the database, and repeat till it runs out of devices to hit.
	
You will need the textfsm files from https://github.com/networktocode/ntc-templates and put the ntc-templates-master folder in the same folder as everything else.

The IPs.txt file is where the crawler will start looking.

To update the network username/password go into explore_network.py and edit the get_username_password function

All the examples assume your using a windows docker build and have your files in d:/network_crawler/network_to_db
	
When I do this  "//d/network_crawler/network_to_db:/network_crawler"  I am just mapping my d:/network_crawler/network_to_db to the linux /network_crawler.  All my files are in d:/network_crawler/network_to_db

	To update the database location/name go into db_work and update there: 
		db_location is the MySQL server
		db_user is the user
		db_password is the database password
		perm_db is the perminate database
		temp_db is the temp database
	

	From main docker:
		Build Images:
			docker build -t network_crawler_build_perm_db:latest -f ./Dockerfile-Build-perm-db .
			docker build -t network_crawler:latest -f ./Dockerfile-Explore-network .
			docker build -t network_crawler_build_reports:latest -f ./Dockerfile-Build-reports .		

	Build the database (only the 1st time this is run)
		docker container run -v //d/network_crawler:/network_crawler network_crawler_build_perm_db
	Crawl the network:
		docker container run -v //d/network_crawler:/network_crawler network_crawler
	Build Reports:
		docker container run -v //d/network_crawler:/network_crawler network_crawler_build_reports
		
		
		
	Popular SQL queries
		select local_host,localInterface,deviceId,interface,ipAddress from cdp;
		select local_host,intf,ipaddr from ip_int_brief;
		select local_host,nexthop_if,network,mask from connected_subnets;
		select local_host,interface,neighbor_id,state,address from ospf;
	
	
	-----------------
	Build a temporary MySQL server for demo purposes (assuming the server is at 192.168.0.183)
	
	Run mysql container on server
	docker run --detach --name=test-mysql --env="MYSQL_ROOT_PASSWORD=password" --publish 3306:3306 mysql
	Connect to server from linux and create the DB
	mysql -u root -p -h 192.168.0.183
	create database network_data_db;
	create database temp_db;
[![published](https://static.production.devnetcloud.com/codeexchange/assets/images/devnet-published.svg)](https://developer.cisco.com/codeexchange/github/repo/GoreNetwork/Network-to-Database)
