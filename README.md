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
From which host do we execute this command?

* On my laptop?
* On a local server?
* On a remote server?
* On which one?

## What credentials do we use?
```
aws_access_key_id = AKIAJ6JWDQIEX37CLFTQ
aws_secret_access_key = nb73XcBE3aW2cTXlVOqeOHBBJnlkPb9Ua
```
* What credentials do we use?
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

