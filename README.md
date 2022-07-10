# AWS Account Setup

In order to execute most steps as CodePipelines and CloudFormation (Infrastrcuture as Code), two S3 buckets are needed.

## Manual Steps
### Create CodePipeline S3 Bucket
This is the bucket used by CodePipeline to unpack the code and perform its work. The bucket should be created in the region where the infrastrucre will be deploed and subsequently where the pipelines and cloudformation scripts will run. 
<ul>
<li>Login to AWS console.</li>
<li>Go to S3 Dashboard.</li>
<li>Click on <b>Create Bucket</b>.</li>
<li>Enter <b>Bucket name</b> as "codepipeline-<aws::region>-<aws::accountid>".</li>
<li>Select <b>AWS Region</b> for bucket, should match the above.</li>
<li>Ensure <b>ACLs disabled</b> is selected.</li>
<li>Ensure <b>Block <em>all</em> public access</b> is ticked.</li>
<li>Ensure <b>Bucket Versioning - Disabled</b> is selected.</li>
<li>Click <b>Add tag</b> and add the following tags.</li>
<ul>
<li><b>Key</b> "ResourceName" - <b>Value</b> "codepipeline-<aws::region>-<aws::accountid>".</li>
<li><b>Key</b> "Creator" - <b>Value</b> "YOUR NAME".</li>
<li><b>Key</b> "Purpose" - <b>Value</b> "This bucket is needed by CodePipeline to build Infrastructure as Code using CloudFormation".</li>
</ul>
<li>Ensure <b>Default Encryption - Enabled</b> is selected.</li>
<li>Ensure <b>Encryption key type - Amazon S3-managed keys (SSE-S3)</b> is selected.</li>
</ul>

### Create Source Code S3 Bucket
This is the bucket where the source code being deployed needs to exist, in order for CodePipeline to access it as part of the build process. 
<ul>
<li>Login to AWS console.</li>
<li>Go to S3 Dashboard.</li>
<li>Click on <b>Create Bucket</b>.</li>
<li>Enter <b>Bucket name</b> as "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Select <b>AWS Region</b> for bucket, should match the above.</li>
<li>Ensure <b>ACLs disabled</b> is selected.</li>
<li>Ensure <b>Block <em>all</em> public access</b> is ticked.</li>
<li>Ensure <b>Bucket Versioning - Enabled</b> is selected.</li>
<li>Click <b>Add tag</b> and add the following tags.</li>
<ul>
<li><b>Key</b> "ResourceName" - <b>Value</b> "cf-source-<aws::region>-<aws::accountid>".</li>
<li><b>Key</b> "Creator" - <b>Value</b> "YOUR NAME".</li>
<li><b>Key</b> "Purpose" - <b>Value</b> "This bucket stores source code and other objects needed to build Infrastructure as Code using CloudFormation".</li>
</ul>
<li>Ensure <b>Default Encryption - Enabled</b> is selected.</li>
<li>Ensure <b>Encryption key type - Amazon S3-managed keys (SSE-S3)</b> is selected.</li>
</ul>

### Create CIS 1.4.0 Security Resources
This has all been scripted to be deployed using Infrastructure as Code. It will create all the resources needed, including a CloudTrai, CloudTrail Bucket, CloudWatch LogGroups, metrics and Alarms. In addition a Lambda is created to check the CIS 1.4 Level 1 status every 6 hours.
<ul>
<li>Login to AWS console.</li>
<li>Go to S3 Dashboard.</li>
<li>Select the source code bucket - "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Drag the "cis140.zip" file containing the code for these resoruces into the bucket.</li>
<li>Click on the <b>Upload</b> button.</li>
<li>Go to CloudFormation Dashboard.</li>
<li>Click on the <b>Create stack</b> button.</li>
<li>Select <b>With new resources (standard)</b>.</li>
<li>Ensure <b>Template is ready</b> is selected.</li>
<li>Select <b>Upload a template file</b>.</li>
<li>Click on the <b>Choose file</b> button.</li>
<li>Select appropriate <b>CodePipelineS3.yaml</b> file.</li>
<li>Click on the <b>Next</b> button.</li>
<li>Enter the <b>Stack name</b> as "csi140-resource-stack".</li>
<li>Enter the <b>PipelineBucket</b> as "codepipeline-<aws::region>-<aws::accountid>".</li>
<li>Enter the <b>PipelineName</b> as "csi140".</li>
<li>Enter the <b>SourceBucket</b> as "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Enter the <b>SourceKey</b> as the name of the file ythat was uploaded into "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Enter the <b>YAMLdir</b> as "CloudFormation".</li>
<li>Click on the <b>Next</b> button.</li>
<li>Click on the <b>Next</b> button.</li>
<li>tick <b>I acknowledge that AWS CloudFormation might create IAM resources.</b>.</li>
<li>Click on the <b>Create stack</b> button.</li>
</ul>
This should create the "csi140-resource-stack", which when complete will create a CodePipeline. This can be found by now doing the following:
<ul>
<li>Go to CodePipeline Dashboard.</li>
<li>Open <b>Pipeline - CodePipeline</b> and click on <b>Pipelines</b>.</li>
<li>Click on the "cis140-codepipeline".</li>
<li>It should be running, if not:</li>
<ul>
<li>Click on the <b>Release change</b> button.</li>
</ul>
</ul>

Once complete, all resources will have been created and the Lambda schedule running. The associated CloudFormation Stacks can be viewed in CloudFormation.

### Create Key Pair
This is a manual step to create a key pair that may be used to access EC2 instances.
<ul>
<li>Login to AWS console.</li>
<li>Go to EC2 Dashboard.</li>
<li>Select Key pairs.</li>
<li>Click <b>Create key pair</b> button.</li>
<li>Enter a <b>Name</b> for the key pair.</li>
<li>Ensure <b>RSA</b> is selected.</li>
<li>Ensure <b>.ppk</b> is selected.</li>
<li>Click <b>Add new tag</b> and add the following tags.</li>
<ul>
<li><b>Key</b> "Owner" - <b>Value</b> "IOP Cloud Analytics Team".</li>
<li><b>Key</b> "ResourceName" - <b>Value</b> the  <b>Name</b> entered above.</li>
<li><b>Key</b> "Creator" - <b>Value</b> "YOUR NAME".</li>
</ul>
<li>Save downloaded key pair.</li>
</ul>

### Create VPC Resources
This has all been scripted to be deployed using Infrastructure as Code. It will create all the resources needed, including a CloudTrai, CloudTrail Bucket, CloudWatch LogGroups, metrics and Alarms. In addition a Lambda is created to check the CIS 1.4 Level 1 status every 6 hours.
<ul>
<li>Login to AWS console</li>
<li>Go to S3 Dashboard</li>
<li>Select the source code bucket - "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Drag the "iopVPC.zip" file containing the code for these resoruces into the bucket.</li>
<li>Click on the <b>Upload</b> button.</li>
<li>Go to CloudFormation Dashboard</li>
<li>Click on the <b>Create stack</b> button.</li>
<li>Select <b>With new resources (standard)</b> .</li>
<li>Ensure <b>Template is ready</b> is selected.</li>
<li>Select <b>Upload a template file</b>.</li>
<li>Click on the <b>Choose file</b> button.</li>
<li>Select appropriate <b>CodePipelineS3.yaml</b> file.</li>
<li>Click on the <b>Next</b> button.</li>
<li>Enter the <b>Stack name</b> as "iopVPC-resource-stack".</li>
<li>Enter the <b>PipelineBucket</b> as "codepipeline-<aws::region>-<aws::accountid>".</li>
<li>Enter the <b>PipelineName</b> as "iopVPC".</li>
<li>Enter the <b>SourceBucket</b> as "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Enter the <b>SourceKey</b> as the name of the file ythat was uploaded into "cf-source-<aws::region>-<aws::accountid>"</li>
<li>Enter the <b>YAMLdir</b> as "CloudFormation"</li>
<li>Click on the <b>Next</b> button.</li>
<li>Click on the <b>Next</b> button.</li>
<li>tick <b>I acknowledge that AWS CloudFormation might create IAM resources.</b>.</li>
<li>Click on the <b>Create stack</b> button.</li>
</ul>
This should create the "iopVPC-resource-stack", which when complete will create a CodePipeline. This can be found by now doing the following:
<ul>
<li>Go to CodePipeline Dashboard</li>
<li>Open <b>Pipeline - CodePipeline</b> and click on <b>Pipelines</b></li>
<li>Click on the "iopVPC-codepipeline".</li>
<li>It should be running, if not</li>
<ul>
<li>Click on the <b>Release change</b> button.</li>
</ul>
</ul>
Once complete, all VPC resources will have been created. The associated CloudFormation Stacks can be viewed in CloudFormation.

### Create Resources to Extract CloudWatch Logs


### Create QRadar User
The access and secret keys for this user will be needed by QRadar to access the CloudTrail logs and CloudWatch logs created above.
<ul>
<li>Login to AWS console</li>
<li>Go to IAM Dashboard</li>
<li>Select Users.</li>
<li>Click <b>Add users</b> button.</li>
<li>Enter a <b>QRadar</b> for the name.</li>
<li>Tick a <b>Access key - Programmatic Access</b>.</li>
<li>Do NOT Tick <b>Password - AWS Management Console Access</b>.</li>
<li>Click <b>Next: Permissions</b> button.</li>
<li>Tick group <b>Cloudtrail-logs-<aws::accountid>-<aws::region></b>.</li>
<li>Tick group <b>Cloudwatch-logs-<aws::accountid>-<aws::region></b>.</li>
<li>Click <b>Next: Tags</b> button.</li>
<li>Enter the following tags.</li>
<ul>
<li><b>Key</b> "Owner" - <b>Value</b> "IOP Cloud Analytics Team".</li>
<li><b>Key</b> "ResourceName" - <b>Value</b> QRadar User.</li>
<li><b>Key</b> "Creator" - <b>Value</b> "YOUR NAME".</li>
</ul>
<li>Click <b>Next: Review</b> button.</li>
<li>Click <b>Create user</b> button.</li>
</ul>

### Send Information to QRadar Team
Fill out spreadsheet and send to QRadar Team.