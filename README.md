# WORDPRESS DEPLOY TO ECS
Procedure of how to deploy **WORDPRESS** to **ECS** by using docker compose.following is the step by step guide: 



## Install and configure ECS-cli

    sudo curl -o /usr/local/bin/ecs-cli https://s3.amazonaws.com/amazon-ecs-cli/ecs-cli-darwin-amd64-latest``

### Apply execute permissions to the binary:
`sudo chmod +x /usr/local/bin/ecs-cli`

To verify ecs -cli is working fine:

    ecs-cli --version

Configure AWS ECS -CLI with AWS credentials , The name of cluster you want to create and the Region as well.

Set up a CLI profile with the following command:

    ecs-cli configure profile --profile-name wordpress --access-key $AWS_ACCESS_KEY_ID --secret-key $AWS_SECRET_ACCESS_KEY

Then, create a cluster configuration:

    $ ecs-cli configure --cluster wordpress --region us-east-1 --default-launch-type EC2 --config-name wordpress

## Create a cluster
The following command will create a cluster with given keypair (ssh key) , t2micro instance(we use it because its free).This clus
   
    ecs-cli up --keypair karan --capability-iam --size 1 --instance-type t2.micro --cluster wordpress

## Create a compose file

we'll create a simple Docker compose file that creates a WordPress application consisting of a web server and a MySQL database.


Here is our compose file (**docker-compose.yml**):

     
    services:
      wordpress:
        image: wordpress
        ports:
          - "80:80"
        links:
          - mysql
      mysql:
        image: mysql:5.7
        environment:
          MYSQL_ROOT_PASSWORD: password

The CPU and memory specifications must be specified separately. Create a file named  **ecs-params.yml**  with the following content:

> task_definition:   services:
>     wordpress:
>       cpu_shares: 100
>       mem_limit: 262144000
>     mysql:
>       cpu_shares: 100
>       mem_limit: 262144000

# Deploy the compose file to a cluster

Now that we created the compose file, we can deploy it to our cluster with the  **ecs-cli compose up**  command. 

    ecs-cli compose up --cluster wordpress --cluster-config wordpress --force-update

	

## View the Running Containers on a Cluster

    ecs-cli ps --cluster wordpress

Now go to AWS console. open ECS service then click your cluster. Here you'll  find EC2 instance in your task. Take its IP put it in browser. You'll able to access you Wrodpress.  
**ALL DONE!!**
