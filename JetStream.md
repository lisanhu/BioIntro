# Using JetStream

## 1. Summary
This section will briefly introduce how we can use Jetstream.

## 2. Requirements
In order to use Jetstream, you must have a valid XSEDE account, and contact the professor to add you to the project. After it's approved, you will receive an email about your account information.

Note: Some information in that email is wrong. The email and many other locations on XSEDE wishes you to use the XSEDE Single Sign-On Login Hub (SSO) or use the Globus Auth. This is wrong because you can find in Jetstream's document that it doesn't support the SSO login hub and it suggests you to use Globus Auth. However, the document is old and Globus Auth is deprecated, so the information is wrong here!

The only way you could communicate with the resource is using [the web interface](https://use.jetstream-cloud.org/).

## 3. System description
Jetstream is a very different cluster from all other clusters you have used. It's not like the others, you will be logging into the login node, and submit jobs through a job scheduler. Jetstream is a virtual machine based system, which means you are accessing virtual machines rather than real computers.

In order to use Jetstream, you will first need to create the virtual machine you need, then wait for a few hours for the virtual machines to be set up. After the virtual machines are successfully set up, you will need to check its status using the web interface and there will be ip address and domain name for each instance of your virtual machines. You can now using the web interface to change your password on the machines and use ssh to access them. You can also set up your ssh public key in your web interface and let the system deploy your ssh credential for each virtual machine.

One thing to consider: The virtual machines will be in on status if you don't do a manually shut down, and it will consume the computation resources! Remeber to turn off your virtual machines after your experiments! The SUs are running out really fast!!

## 4. Workflow
The workflow to use Jetstream:

One time:
1. Use your credentials to login into [the web interface](https://use.jetstream-cloud.org)
2. Create virtual machines through the interface
3. Wait until it's fully set up and set up your password or ssh key through the web shell

Every time:
0. On the web interface, turn on your virtual machines
1. You can ssh into your system
2. You can grant yourself sudo priviledge
3. Do experiments and everything like in your own computer
4. Remeber to shutdown the virtual machine after your job is done
5. You can google how to set up email server to send yourself an email after a particular job is done
6. You can also put a shutdown command after your job script.
7. It's recommended to do both 5 and 6, so you can check whether the virtual machines are successfully shut down (it might be interrupted).

## 5. Practice
Create a virtual machine, try to install some of the bio tools on it.


[Previous](InstallTools.md)