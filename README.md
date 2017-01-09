# Cloudfuzzer

Cloudfuzzer is a cloud fuzzing framework. Purpose of cloudfuzzer is to make it possible to easily run automated fuzz-testing in cloud environment.

In cloud environment __bastion__ instance works as a SSH gateway between outside world and fuzzing cluster. It is used to deliver docker image from user to swarm machines. Bastion is also used for storing fuzzing results. __Fuzzvm__ instances consist of one __swarm-master__ and __N__ __swarm-nodes__. Swarm-master is used to set up docker-swarm, including all swarm-machine instances. It runs docker swarm discovery service and distributes fuzzing jobs, once received from Bastion. Swarm-nodes run fuzzing docker containers and sync results with bastion. Swarm-nodes can be run as Preemptible/SpotInstance instances because they have shutdown detection and they sync results before shutdown.

__Note:__ Cloudfuzzer nodes are not supposed to be visible in public network. No TLS is used in them and docker daemon can be accessed from network. They should be connected via bastion.

# Getting started

Packer is used for creating images to cloud environment. You must build images for bastion and fuzzvm. You find the packer files from [packer/](packer/) directory.

By default, packer files for bastion and fuzzvm use use_variables for account_file and project_id.

One way to use them is to make a separate json-file:
```
{
    "account_file":	"/path/to/your/account_file.json",
    "project_id":	"your_cloudfuzzer_project_id"
}
```

After that you can build images with following commands (Google Cloud)

bastion
```
packer build -only=gcloud -var-file=/path/to/your/variables.json packer-bastion.json
```

fuzzvm
```
packer build -only=gcloud -var-file=/path/to/your/variables.json packer-fuzzvm.json
```

* If you want to use aws use -only=aws
* You can use -force if you want Packer to rewrite existing images in cloud platform.
* Using Google Compute Engine with Packer: https://www.packer.io/docs/builders/googlecompute.html

# Requirements:
* [Packer](https://www.packer.io/) 0.11.0
* Cloud service

License
----
Apache 2.0
