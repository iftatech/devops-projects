Build a 3-Tier Architecture with Docker Swarm on Aws

![image](https://github.com/abiolashittu-org/docker-swarm-3tier-architecture/assets/108594160/70f274ba-7e6a-4c7b-b65f-7326d12c466c)



## Tasks



Using AWS, create a Docker Swarm that consists of one manager and three worker nodes. Verify the cluster is working by deploying the following three-tier architecture:

- A service based on the Redis Docker image with 4 replicas.
- A service based on the Apache Docker image with 10 replicas.
- A service based on the Postgres Docker image with 1 replica.

## Create the Manager Node

1. Navigate your AWS Management Console to EC2, and press ‘Launch Instance’ to create a new EC2 instance.
2. Select Amazon Linux 2 AMI for the instance and choose an instance type. For our project, we will use a `t2.micro`, but depending on your use case, you may want to use a `t2.medium` or an instance with more resources for the manager node. The performance of our swarm cluster may be limited by the resources of the manager node.
3. Configure the rest of the instance details. It is helpful to add a tag for later identification.

### Launch the Instance

We can now connect to our instance via SSH.

We will connect to our instance with the following command which is provided to us:

```bash
ssh -i "abi.pem" ec2-user@ec2-33-37-93-157.compute-1.amazonaws.com
```

The above is specific to my project’s instance, but you may fill in the code below with your own instance’s details by following these guidelines:

```bash
ssh -i <path-to-your-private-key-file> <your-username>@<public-ip-address>
```

Connect and run any updates requested.

We are now connected to our instance. Once logged in, we can install Docker Swarm. To do this on Amazon Linux, we will run the following commands:

```bash
# Update with the latest patches 
sudo yum update -y

# Install Docker from the Amazon Linux Extras repository
sudo amazon-linux-extras install docker -y

# Start Docker 
sudo service docker start

# Add the ec2-user to the docker group to run Docker commands without using sudo
sudo usermod -a -G docker ec2-user
```

We can then run `docker version` to verify that Docker was successfully installed. However, we also got this error message! This is because the user (us) running the `docker version` command does not have permission to access the Docker daemon. Typically the user is added to this group by default when installing Docker. We can fix this however by adding ourselves to the Docker group manually with:

```bash
sudo usermod -aG docker ec2-user
```

We then need to log out and back in to apply the group membership update:

```bash
exit
ssh -i "abi.pem" ec2-user@ec2-33-37-93-157.compute-1.amazonaws.com
```

We are now able to run `docker version` without receiving the error and can verify with `groups` that we are part of the “docker” group.

## Initialize the Docker Swarm

With Docker installed and correct permissions, we are now able to initialize the Docker Swarm.

We will do so using the following command:

```bash
sudo docker swarm init --advertise-addr <private-ip-address>
```

First, we must find the private IP of our instance. We can do this through `ifconfig`:

```bash
ifconfig
```

In this specific project, we already knew our IP because our Amazon EC2 username by default was `ec2-user@33.37.93.157`. However, knowing this command is useful anyway to verify and in case it is not always located in our username.

Now that we have the private IP, we can use it in the above command to initialize the Docker Swarm:

```bash
sudo docker swarm init --advertise-addr 33.37.93.157
```

We enter this and are given this output when finished:

We want to save this join token that we are given so that we are able to join worker nodes to the Docker Swarm.

Our manager node is now set up! We can now proceed to create our worker nodes and set up our three-tier architecture.

## Setting up the Worker Nodes (Three-Tier Architecture)

We are now going to set up three more Amazon EC2 instances to serve as our worker nodes.

### Node 1 — Redis

We will follow similar steps as shown above when we created our manager node instance.

We must also connect to each via SSH:

```bash
ssh -i "abi.pem" ec2-user@<worker-node-public-ip>
```

Now we will install Docker:

```bash
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -a -G docker ec2-user
```

Repeat the process of granting permissions. There may be an issue with our original command but for now the following command is doing what we need it to do. We will try replacing the last line in our Docker install with this for the next node and see if that corrects it without the extra step.

```bash
sudo usermod -aG docker ec2-user
```

Great! Worker Node 1 is up and running. Let’s repeat the process with nodes 2 & 3. It’s best to have all three nodes running before we start connecting them to our swarm.

### Node 2 — Apache

This time let’s adjust that final line in our install code and see if it saves us a step in the process:

```bash
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -aG docker ec2-user
```

We still needed to log out and back in, but looks like we are good now.

Node 2 is good, now on to Node 3.

As an aside, I have been opening up a new terminal tab for each node in VS Code to keep organized as we open these different instances.

### Node 3 — Postgres

Complete! Now that we have our three nodes up and running with Docker installed on each, we are ready to join them to the swarm.

## Set Up the Swarm

Let's revisit that output that we got when we set up the manager node:

As shown above, we can add workers to the swarm by running the following command on each worker node:

```bash
sudo docker swarm join --token SWMTKN-1-4zxlb8h4i5rjf6jk3t2q3m4n5v8w9lxy2r7a1h0c3p0d9b6v0-1c2d3e4f5g6h7i8j9k0lmnopqrstuvw
 180.42.63.72:2377
```

If you would like the generic code to do this on your own machine, I will provide it below:

```bash
sudo docker swarm join --token <worker-token> <manager-private-ip>:2377
```

Just replace the `<worker-token>` with the token provided when your manager node was set up, and the `<manager-private-ip>` with your manager node’s private IP.

This will add the worker node to the Swarm. Note: Our nodes were not joining the manager node initially due to security group issues. 

It was necessary to create a security group to accept incoming connections on port 2377. I added the IP addresses of each of the three worker nodes and then attached the security group to the manager node instance.

We can now see that if we go to our manager node and run `docker node ls` that all 3 of our worker nodes are connected to our swarm, and our manager node is listed as the “Leader”:

## Build the Three-Tier Architecture

We can run the following commands in the SSH terminal of the manager node. This will create three services with the required number of replicas.

```bash
# Create a Redis service with 4 replicas
docker service create --name redis --replicas 4 redis

# Create an Apache service with 10 replicas
docker service create --name apache --replicas 10 httpd

# Create a Postgres service with 1 replica
docker service create --name postgres --replicas 1 -e POSTGRES_PASSWORD=password postgres
```

We can check the status of our services with:

```bash
docker service ls
```

To list the running containers, their status, names, and ports:

```bash
docker service ps
```

To check a particular service in the swarm:

```bash
docker service ps apache

docker service ps postgres

docker service ps redis
```

Congratulations! We have successfully set up a Docker Swarm with a manager node and three worker nodes and made a three-tiered architecture with Redis, Apache, and Postgres. Stay tuned for the next part in this series as we venture deeper into Docker!

---

**Tags:** Docker, AWS, Docker Swarm, Apache, Redis, Postgres
