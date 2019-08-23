# Terraform, XL Release and XL Deploy
Welcome to this tutorial and thank you for reading!. We are going to talk about how to manage infrastructures in the Cloud (we will see how to do it on Amazon) using Terraform, and do it in an agile, fast, and secure way, and always maintaining auditability of the processes followed and traceability of all the changes applied.

## What do we face?
If we want to have our infrastructure defined as code and we start using Terraform to provision that infrastructure in the cloud, for example in AWS, we will have to describe the resources (auto scaling group, elb, security groups, etc.) in different files, e.g.

```
resource "aws_instance" "front" {
  ami = "${data.aws_ami.ubuntu.id}"
  instance_type = "${var.instance_type}"
  key_name = "${aws_key_pair.akp.id}"
  subnet_id = "${aws_subnet.public.id}"
  vpc_security_group_ids = [
      "${aws_security_group.allow_22.id}",
      "${aws_security_group.allow_8080.id}"
    ]
  tags = {
    Name = "${var.project_name}-${var.environment}-front"
  }
}
resource "aws_instance" "bdd" {
  ami = "${data.aws_ami.ubuntu.id}"
  instance_type = "${var.instance_type}"
  key_name = "${aws_key_pair.akp.id}"
  subnet_id = "${aws_subnet.public.id}"
  vpc_security_group_ids = [
      "${aws_security_group.allow_22.id}",
      "${aws_security_group.allow_3306.id}"
    ]
  tags = {
    Name = "${var.project_name}-${var.environment}-bdd"
  }
}
```

Now, **how do we apply these resources?**

## Easy!
Easy!, it is all about executing the Terraform client from the directory where our templates are located and we will have our resources created in the cloud.
```
$ terraform apply
```
But ... **one more thing**.

## From which host do we execute this command?
* On my laptop?
* On a local server?
* On a remote server?
* On which one?

## What credentials do we use?
```
aws_access_key_id = AKIAJ6JWDQIEX37CLFTQ
aws_secret_access_key = nb73XcBE3aW2cTXlVOqeOHBBJnlkPb9Ua
```
* Where are those keys?
* Who has access to them?
* How do we protect them?

## What kind of environment am I provisioning?
Surely we want to use these Terraform templates to provision several environments so ...
* how do we know which environment we are creating or updating?
* development?
* testing?
* production?

## What parameters am I using in each one?
We probably want to reuse our code and apply the Terraform templates we have created to provision different environments. And this forces us to parameterize certain information. For example:
* in development we will want fewer and smaller EC2 instances than in production.
* we will want to install different public keys to access the hosts that we create in different environments

But ...
* How do I manage the different values of those parameters for different environments?
* Where do I register them?
* How do I know what parameters were used to provision an environment?
* How do I see the different values between different environments?
* How do I keep track of the changes I made in the configuration of a given environment?

## Where do I store the Terraform state file?
The state file is critical when I am provisioning infrastructure with Terraform, because it maintains the current state of that infrastructure and is key to apply changes.
* Where is the Terraform status file?
* What is the correct directory from which I must execute the command?

**This is very important**, if we execute some templates from the wrong directory, what we can do is **destroy an entire infrastructure**.
Therefore ...
* Do I store that state file locally?
* Remotely in an S3 bucket, for example?
* If I store it locally, in which directory?, and how do I ensure that all the changes that apply to that infrastructure are going to be executed from the same directory?
* If I store it remotely, what path should I use on S3?

## Do we trust the content of these templates?
* Are we sure that we will create the necessary resources in AWS?
* Are we sure that these files have been reviewed by the right team?
* Are we wure that they comply with the security requirements imposed?
* Do the templates follow the best recommended practices?

## Have we tried these Terraform templates in previous environments?
* Are they correctly parameterized and I am sure that I can reuse them to provision the different environments, from development to production?
* And am I sure that when provisioning the infrastructure in production I will have the same results as those obtained in previous environments?

## Who, when, how and why?
* How do we know who created the infrastructure?
* Do we know when it was created?
* Do we know what parameters were used?
* Do we know what motivated the creation of that infrastructure?, should we maintain traceability with a ticketing system like Jira?

## How do we get the data from the infrastructure?
Once we have created the new infrastructure, how do we obtain the data to access it as the IP address of the different hosts that have been created?

Of course, there is a way to obtain this information by accessing the Amazon console or seeing the variables that we have configured as 'outputs' in Terraform templates. But how to treat that information automatically to, for example, invoke Ansible playbooks and provision the newly created hosts?

## And how to destroy an infrastructure?
We have to provide the same parameters that we use to create it.
* Do we know what parameters we used?
* From which directory do we have to launch the command 'terraform destroy'?
* Are we sure we are going to destroy the correct environment?

## And this is for just one application
Therefore, to provision several environments (development, testing, production) in the cloud **for a single application, we have to take into account:**
* What templates are going to be used
* How to ensure that these templates have been validated by the right teams
* Hosts where the commands have to be executed
* How credentials will be managed
* How the different parameters are going to be managed
* What public keys are going to be installed on the remote servers
* How the process will be audited to know who, when, how and why.
* Where to keep the state file and how to ensure that executions take place in the correct host and directory.
* How to do the deprovisioning of an infrastructure applying the right parameters.

**What if you have to do this for one hundred applications?**

## We have a challenge to address and solve
* Template versioning
* Apply them
* Parameterization
* Auditability
* Traceability
* etc.

We have to find a solution that **scales** and that responds to the problems we have raised.