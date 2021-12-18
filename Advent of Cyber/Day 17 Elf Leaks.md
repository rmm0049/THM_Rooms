# Elf Leaks
### Shadow IT
Sometimes business units go around corporate IT, procurement, legal and security when they need to get the job done quickly. This leads to security teams not knowing what they need to protect and systems not being build to IT or security standards. Most accessible public cloud is AWS.

### Learning Objective
Covering basics of AWS, two of the most common services -> Amazon S3 (Simple Storage Service) and AWS IAM (Identity and Access Management)

### Amazon AWS
AWS divides its infrastructure into Regions, mostly independent clusters of datacenters. Within each region are availability zones (AZ). Each AZ in a region leverages separate power grids and usually are located in different flood plains. The redundancy allows highly resilient architectures to withstand significant weather or geological events, or more frequently, hardware or facility failures.

You can specify a region with the `--region` option in AWS CLI

### Amazon S3
This is Amazon's hosted object storage service. Objects are stored in Buckets. To highly simplify, Buckets are key-value stores with the Object Key being a full pathname for a file and the value being the contents of the file. S3 is publicly hosted service - it doesn't exist behind a corporate firewall, making it convienent for hosting public content. AWS Buckets use a global namespace. 

Only one AWS customer can create a bucket named `bestfestivalcompany-images`

### Discovering Bucket Names
There are many ways to discover the names of Buckets. One of the easiest ways is when a company embeds content hosted in S3 on their website. Images PDFs, etc., can all be hosted cheaply in S3 and linked from another site. These links will look like this

`http://BUCKETNAME.s3.amazonaws.co/FILENAME.ext`
or
`http://s3.amazonaws.com/BUCKETNAME/FILENAME.ext`

###### Listing the Contents of Buckets
Amazon S3 is one of AWS's oldest services. It's so old that it has two different methods of access control: Bucket Policies and S3 ACLs. This leads to great confusion for devlopers who must manage policies, ACLs, and the differences between Any User and Authenticated Users.

Many Buckets that contain public information allow you to list the contents of the bucket. 

`curl http://irs-form-990.s3.amazonaws.com/`

will return a pile of XML of all the IRS Form 990 filings for US Tax-Exempt corporations.

The AWS CLI also provides the ability to list the contents of a bucket
`aws s3 ls s3://irs-form-990/ --no-sign-request`
The option `--no-sign-request` allows you to request data from S3 without being an AWS Customer.

##### Downloading Objects
Downloading an object from S3 is also easy. You can use *curl*:
`curl http://irs-form-990.s3.amazonaws.com/201101319349101615_public.xml`
or the AWS CLI:
`aws s3 cp s3://irs-form-990/201101319349101615_public.xml . --no-sign-request`
Note the two different URIs for an object. Objects can be addressed with http:// or via s3://

### The different levels of Amazon S3 Authentication
In Amazon S3, permissions are different from Bucket permissions. Bucket permissions allow you to list the objects in a bucket, while the object's permissions will enable you to download the object. In the case of the irs-form-990 bucket, both the bucket and all the objects in the bucket are publicly readable. Objects can be readable while the buckets is not, or the bucket can be publicly readable, but the Objects are not.

There are two levels of public objects and buckets. The first level is "Anyone" as in the IRS form bucket. The second level is just as public - and that is public to any AWS Customer. Anyone with a credit card can create an AWS account, so not too much data protection.

### AWS IAM
Excluding a few older services like Amazon S3, all requests to AWS services must be signed. This is typically done behind the scenes by the AWS CLI or the various Software development Kits that AWS provides. The signing process leverages IAM Access Keys. These keys are the primary ways an AWS account is compromised.

###### IAM Access Keys
IAM Access Keys consist of an Access Key ID and the Secret Access Key. Access Key IDs alwasy begin with letters `AKIA` and are 20 characters long. These act as a user name for the AWS API. The Secret Access Key is 40 characters long. AWS generates both strings; however, AWS doesn't make the Secret Access Key available to download after the initial generation.

There is another type of credentials, short-term credentials, where the Access Key ID begins with the letters ASIA and includes an additional string called the Session Token. Conducting Reconnaissance with IAM

When you find credentials to AWS, you can add them to your AWS Profile in the AWS CLI. For this you can you the command:
`aws configure --profile PROFILENAME`

This command will add entries to the `.aws/config` and `.aws/credentials` files in your user's home directory.

Once you have configured a new profile with the new access keys, you can execute any command using this other set of credentials. For example, to list all the S3 Buckets in the AWS account you have found credentials for, try:
`aws s3 ls --profile PROFILENAME`

A few other common AWS reconnaisance techniques are:
1. Finding the Account ID belonging to an access key:
	`aws sts get-access-key-info --access-key-id AKIAEXAMPLE`
2. Determing the username the access key you're using belongs to
	`aws sts get-caller-identity --profile PROFILENAME`
3. Listing all the EC2 instances running on an account
	`aws ec2 describe-instances --output text --profile PROFILENAME`
4. Listing all the EC2 instances running in an account in a different region
	`aws ec2 describe-instances --output text --region us-east-1 --profile PROFILENAME`

###### AWS ARNs
The Amazon ARN is their way of generating unique identifier for all resources in the AWS Cloud. it consists of multiple strings separated by colons. The format is
`arn:aws:<service>:<region>:<account_id>:<resource_type>/<resource_name>`

### Challenge
Somehow, the Grinch has managed to get hold of all the Elves' names and email addresses. How could this have happened? Given the scope of the breach, McSkidy believes someone in HR must be involved. You know that HR recently launched a new portal site using WordPress. You also know that HR didn't request any infrastructure from IT to deploy this portal site. Where is that portal hosted?
