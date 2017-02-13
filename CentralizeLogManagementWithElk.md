
The following section covers the sequence involved in starting the 
elk contianer configuring the logstash with /data/logstash/tcp.conf
file that allow logstash to listen to the logs coming on 5000/tcp 
port of the elk container and forward them to the 9200 port of 
the container to get the logs indexed by elastic search
------------------------------

to print the logs from the elk docker container
# sudo docker logs elk

# now with elk running ,attach to the container shell by executing 
  103  sudo docker exec -it elk /bin/bash

# the following command now stops the elk
  104  sudo docker stop elk


# to remove the container issue
  105  sudo docker rm elk

Now need to create a syslog configuration file for hte logstash.
Create a directory /data/logstash in host and create a syslog.conf file

  106  cd /data/logstash
  107  ls
  108  sudo ren syslog.conf 99-syslog.conf
  109  sudo rename syslog.conf 99-syslog.conf
  110  ls
  111  sudo rename syslog.conf 99-syslog.conf
  112  sudo cp syslog.conf /data
  113  cd ..
  114  ls
  115  cd logstash
  116  ls
  117  sudo rm syslog.conf 

=== The /data/logstash is mounted at the /etc/logstash/conf.d directory of container running logstash.
Now with the this mounted directory /data/logstash has only the syslgo.con file and and it loads with that.
 



 118  sudo docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -p 5000:5000 -v /data/logstash:/etc/logstash/conf.d -it --name elk sebp/elk
  119  nc localhost 5000 < /var/log/dpkg.log
  
==== However an attempt to define the p 5000/udp and associate it with syslog doesnt work well. Now lets try with the tcp config

120  sudo docker exec -it elk /bin/bash
  121  sudo docker stop elk
  122  sudo docker rm elk
  123  sudo gedit /data/logstash/tcp.conf 
 

== Created a/data/logstash/tcp.conf with the following configuraiton

tcp.conf
------------
osboxes@osboxes:/data/logstash$ cat tcp.conf 
input {
	tcp {	port => 5000	}
}
	

## Add your filters / logstash plugins configuration here
	
output {
	elasticsearch { hosts => ["localhost:9200"] }
	stdout { codec => rubydebug }

	
}
Now what this declares is a tcp input port @5000 and output to localhost:9200 the elasticsearch

Now we can search whether someone is listening on the port 5000

osboxes@osboxes:/data/logstash$ netstat -an | grep 5000
tcp6       0      0 :::5000                 :::*                    LISTEN  

With now elk running ,try to do a bulk data write to 5000 using nc


  126  nc localhost 5000 < /var/log/dpkg.log
  127  nc localhost 5000 < /var/log/Xorg.0.log
  128  cat /var/log/Xorg.0.log

This now shows the data is written to logstash

============================================================

The following exercise was done to configure the rsyslog in the ubuntu host to accept 
logs over tcp/udp @ default 514. To make this funcitonal enable the streaming input by
configuring the /etc/syslog.conf

-----
osboxes@osboxes:/etc/rsyslog.d$ cat /etc/rsyslog.conf
#  /etc/rsyslog.conf	Configuration file for rsyslog.
#
#			For more information see
#			/usr/share/doc/rsyslog-doc/html/rsyslog_conf.html
#
#  Default logging rules can be found in /etc/rsyslog.d/50-default.conf


#################
#### MODULES ####
#################

module(load="imuxsock") # provides support for local system logging
module(load="imklog")   # provides kernel logging support
#module(load="immark")  # provides --MARK-- message capability

# provides UDP syslog reception
module(load="imudp")
input(type="imudp" port="514")

# provides TCP syslog reception
module(load="imtcp")
input(type="imtcp" port="514")


==========================
Restart systemctl restart rsyslog

---------------------------

Oct 14 13:18:19 osboxes docker/0098bc85e1cd[9551]: This is a test
root@osboxes:/etc# sudo docker run -t -d  --log-driver=syslog --log-opt syslog-address=tcp://localhost:514 ubuntu  echo "This is a test 123"
e089853aae03b24907154285b19b416c0099ff3859511a0faeb9fef295eb4274
root@osboxes:/etc# cat /var/log/syslog | grep This

Oct 14 13:16:45 localhost docker/1c236792e21e[9551]: This is a test#015
Oct 14 13:18:19 osboxes docker/0098bc85e1cd[9551]: This is a test
Oct 14 13:22:32 localhost docker/021aa50ce718[9551]: message repeated 2 times: [ This is a test#015]
Oct 14 13:25:06 localhost docker/e089853aae03[9551]: This is a test 123#015
root@osboxes:/etc# 

===See all the logs from teh docker containers.....


