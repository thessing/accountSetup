# AWS Account Setup

In order to execute most steps as CodePipelines and CloudFormation (Infrastrcuture as Code), two S3 buckets are needed.

## Manual Steps
### Create CodePipeline S3 Bucket
This is the bucket used by CodePipeline to unpack the code and perform its work. The bucket should be created in the region where the infrastrucre will be deploed and subsequently where the pipelines and cloudformation scripts will run. 
<ul>
<li>Login to AWS console</li>
<li>Go to S3 Dashboard</li>
<li>Click on **Create Bucket**</li>
<li>Enter **Bucket name** as "codepipeline-<aws::region>-<aws::accountid>"</li>
<li>Select **AWS Region** for bucket, should match the above.</li>
<li>Ensure **ACLs disabled** is selected.</li>
<li>Ensure **Block <em>all</em> public access** is ticked.</li>
<li>Ensure **Bucket Versioning - Disabled** is selected.</li>
<li>Click **Add tag** and add the following tags.</li>
<ul>
<li>**Key** "ResourceName" - **Value** "codepipeline-<aws::region>-<aws::accountid>" </li>
<li>**Key** "Creator" - **Value** "YOUR NAME" </li>
<li>**Key** "Purpose" - **Value** "This bucket is needed by CodePipeline to build Infrastructure as Code using CloudFormation." </li>
</ul>
<li>Ensure **Default Encryption - Enabled** is selected.</li>
<li>Ensure **Encryption key type - Amazon S3-managed keys (SSE-S3)** is selected.</li>
</ul>

### Create Source Code S3 Bucket
This is the bucket where the source code being deployed needs to exist, in order for CodePipeline to access it as part of the build process. 
<ul>
<li>Login to AWS console</li>
<li>Go to S3 Dashboard</li>
<li>Click on **Create Bucket**</li>
<li>Enter **Bucket name** as "cf-source-<aws::region>-<aws::accountid>"</li>
<li>Select **AWS Region** for bucket, should match the above.</li>
<li>Ensure **ACLs disabled** is selected.</li>
<li>Ensure **Block <em>all</em> public access** is ticked.</li>
<li>Ensure **Bucket Versioning - Enabled** is selected.</li>
<li>Click **Add tag** and add the following tags.</li>
<ul>
<li>**Key** "ResourceName" - **Value** "cf-source-<aws::region>-<aws::accountid>" </li>
<li>**Key** "Creator" - **Value** "YOUR NAME" </li>
<li>**Key** "Purpose" - **Value** "This bucket stores source code and other objects needed to build Infrastructure as Code using CloudFormation." </li>
</ul>
<li>Ensure **Default Encryption - Enabled** is selected.</li>
<li>Ensure **Encryption key type - Amazon S3-managed keys (SSE-S3)** is selected.</li>
</ul>

### Create CIS 1.4.0 Security Resources
This has all been scrpted to be deployed using Infrastructure as Code. It will create all the resources needed, including a CloudTrai, CloudTrail Bucket, CloudWatch LogGroups, metrics and Alarms. In addition a Lambda is created to check the CIS 1.4 Level 1 status every 6 hours.
<ul>
<li>Login to AWS console</li>
<li>Go to S3 Dashboard</li>
<li>Select the source code bucket - "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Drag the "cis140.zip" file containing the code for these resoruces into the bucket.</li>
<li>Click on the **Upload** button.</li>
<li>Go to CloudFormation Dashboard</li>
<li>Click on the **Create stack** button.</li>
<li>Select **With new resources (standard)** .</li>
<li>Ensure **Template is ready** is selected.</li>
<li>Select **Upload a template file**.</li>
<li>Click on the **Choose file** button.</li>
<li>Select appropriate **CodePipelineS3.yaml** file.</li>
<li>Click on the **Next** button.</li>
<li>Enter the **Stack name** as "csi140-resource-stack".</li>
<li>Enter the **PipelineBucket** as "codepipeline-<aws::region>-<aws::accountid>".</li>
<li>Enter the **PipelineName** as "csi140".</li>
<li>Enter the **SourceBucket** as "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Enter the **SourceKey** as the name of the file ythat was uploaded into "cf-source-<aws::region>-<aws::accountid>"</li>
<li>Enter the **YAMLdir** as "CloudFormation"</li>
<li>Click on the **Next** button.</li>
<li>Click on the **Next** button.</li>
<li>tick **I acknowledge that AWS CloudFormation might create IAM resources.**.</li>
<li>Click on the **Create stack** button.</li>
</ul>
This should create the "csi140-resource-stack", which when complete will create a CodePipeline. This can be found by now doing the following:
<ul>
<li>Go to CodePipeline Dashboard</li>
<li>Open **Pipeline - CodePipeline** and click on **Pipelines**</li>
<li>Click on the "cis140-codepipeline".</li>
<li>It should be running, if not</li>
<ul>
<li>Click on the **Release change** button.</li>
</ul>
</ul>
Once complete, all resources will have been created and the Lambda schedule running. The associated CloudFormation Stacks can be viewed in CloudFormation.
