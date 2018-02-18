# ansible-f5-aws-lab
Setting up a cloud lab environment (Amazon Web Services) using Ansible :sunglasses:
## Purpose
The purpose of this Ansible playbook is to setup a ready-to-use cloud lab environment for F5 BIG-IP training and development. A client connects to the RDP Jumphost (DNS record automatically set via Cloudflare API), from where he can access both the F5 BIG-IP as well as the Webserver farm. 

The environment consists of the following parts:
* Virtual Private Cloud (VPC) with three subnets
  * Management subnet
  * External (client-side) subnet
  * Internal (server-side) subnet
* Windows Server 2012 R2 machine acting as RDP Jumphost
* F5 BIG-IP ADC
* Webserver farm with three LAMP servers 

## Requirements
* Ansible 2.4
* Knowledge in AWS :wink:
## Usage
1. Edit `public.yaml` variable file according to your needs
2. Create a new `secret.yaml` variable file: `ansible-vault create secret.yaml`
```yaml
aws_access_key: XXXXYYYYZZZZ
aws_secret_key: XXXXYYYYZZZZXXXXYYYYZZZZ
win_initial_password: TopSecretPassword
f5_admin_password: TopSecretPassword
f5_root_password: TopSecretPassword
cf_api_key: XXXXYYYYZZZZXXXXYYYYZZZZ
cf_zone_id: XXXXYYYYZZZZXXXXYYYYZZZZ
```
3. Edit cloud-init templates (`templates` folder) according to your needs
4. Run the playbook: `ansible-playbook -i hosts main.yaml --ask-vault-pass --extra-vars "env=Student01"`

## AMI Images used
- Up-to-date Windows_Server-2012-R2_RTM-English-64Bit-Base image
- Jetware LAMP stack
  - https://aws.amazon.com/marketplace/pp/B01N2PDQ6M?qid=1518913849685&sr=0-7
  - ami-800fd1ef
- F5 Prelicensed Hourly BIGIP-13.1.0.2.0.0.6 - Best 25MBPS
  - https://aws.amazon.com/marketplace/pp/B079C4WR32?qid=1518914175494&sr=0-1
  - ami-a8ddbbc7

## Some notes
- Using `--extra-vars "env=Student01` you can setup multiple lab environments, perfect for customer trainings.
- The Windows Jumphost uses a special cloud-init config so enable the WinRM interface for software management. I used the `Chocolatey` Package Manager (https://chocolatey.org/) for installing software. 
- The LAMP server configuration is done via the cloud-init file. 
- I decided to go with the "manual" way in regards of BIG-IP deployment. The AWS SSH Key is needed to initially connect to the BIG-IP for the `admin` password change. Using the official CloudFormation templates (https://github.com/F5Networks/f5-aws-cloudformation) instead of doing it manually is also an option, you may want to use a combination of CloudFormation with some own cloud-init script. 
- I decided to go with the "manual" way in regards of BIG-IP deployment. You can also use the official CloudFormation templates (https://github.com/F5Networks/f5-aws-cloudformation), maybe in combination with some own cloud-init script. 
- DNS is nice-to-have, you can use every API-capable DNS provider you like. 
