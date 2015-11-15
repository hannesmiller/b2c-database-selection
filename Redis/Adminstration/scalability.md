In this section we will talk about how good and easy is Redis when it comes to scalability. The section will be divided into four parts to explain how to scale reads, writes , configurations, and complex queries.

### Scaling Reads

When your system grows bigger and the system read throughput increases more than what Redis server can handle, then you must scale your server to ensure that reads are as fast as possible.  






````
slaveof 192.168.1.1 6379
````

The above IP is the IP of your master server. Also you can issue the command SLAVEOF from the slave server in the same way to make it listen to a master server. If you want to stop the replication and make the slave server acts as a master, you can issue the below command:

````
SLAVEOF NO ONE


