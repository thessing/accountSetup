# AWS Account Setup

In order to execute most steps as CodePipelines and CloudFormation (Infrastrcuture as Code), two S3 buckets are needed.

## Manual Steps
### Create CodePipeline S3 Bucket
This is the bucket used by CodePipeline to unpack the code and perform its work. The bucket should be created in the region where the infrastrucre will be deploed and subsequently where the pipelines and cloudformation scripts will run. 
<ul>
<li>Login to AWS console</li>
<li>Go to S3 Dashboard</li>
<li>Click on <b>Create Bucket</b></li>
<li>Enter <b>Bucket name</b> as "codepipeline-<aws::region>-<aws::accountid>"</li>
<li>Select <b>AWS Region</b> for bucket, should match the above.</li>
<li>Ensure <b>ACLs disabled</b> is selected.</li>
<li>Ensure <b>Block <em>all</em> public access</b> is ticked.</li>
<li>Ensure <b>Bucket Versioning - Disabled</b> is selected.</li>
<li>Click <b>Add tag</b> and add the following tags.</li>
<ul>
<li><b>Key</b> "ResourceName" - <b>Value</b> "codepipeline-<aws::region>-<aws::accountid>" </li>
<li><b>Key</b> "Creator" - <b>Value</b> "YOUR NAME" </li>
<li><b>Key</b> "Purpose" - <b>Value</b> "This bucket is needed by CodePipeline to build Infrastructure as Code using CloudFormation." </li>
</ul>
<li>Ensure <b>Default Encryption - Enabled</b> is selected.</li>
<li>Ensure <b>Encryption key type - Amazon S3-managed keys (SSE-S3)</b> is selected.</li>
</ul>

### Create Source Code S3 Bucket
This is the bucket where the source code being deployed needs to exist, in order for CodePipeline to access it as part of the build process. 
<ul>
<li>Login to AWS console</li>
<li>Go to S3 Dashboard</li>
<li>Click on <b>Create Bucket</b></li>
<li>Enter <b>Bucket name</b> as "cf-source-<aws::region>-<aws::accountid>"</li>
<li>Select <b>AWS Region</b> for bucket, should match the above.</li>
<li>Ensure <b>ACLs disabled</b> is selected.</li>
<li>Ensure <b>Block <em>all</em> public access</b> is ticked.</li>
<li>Ensure <b>Bucket Versioning - Enabled</b> is selected.</li>
<li>Click <b>Add tag</b> and add the following tags.</li>
<ul>
<li><b>Key</b> "ResourceName" - <b>Value</b> "cf-source-<aws::region>-<aws::accountid>" </li>
<li><b>Key</b> "Creator" - <b>Value</b> "YOUR NAME" </li>
<li><b>Key</b> "Purpose" - <b>Value</b> "This bucket stores source code and other objects needed to build Infrastructure as Code using CloudFormation." </li>
</ul>
<li>Ensure <b>Default Encryption - Enabled</b> is selected.</li>
<li>Ensure <b>Encryption key type - Amazon S3-managed keys (SSE-S3)</b> is selected.</li>
</ul>

### Create CIS 1.4.0 Security Resources
This has all been scrpted to be deployed using Infrastructure as Code. It will create all the resources needed, including a CloudTrai, CloudTrail Bucket, CloudWatch LogGroups, metrics and Alarms. In addition a Lambda is created to check the CIS 1.4 Level 1 status every 6 hours.
<ul>
<li>Login to AWS console</li>
<li>Go to S3 Dashboard</li>
<li>Select the source code bucket - "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Drag the "cis140.zip" file containing the code for these resoruces into the bucket.</li>
<li>Click on the <b>Upload</b> button.</li>
<li>Go to CloudFormation Dashboard</li>
<li>Click on the <b>Create stack</b> button.</li>
<li>Select <b>With new resources (standard)</b> .</li>
<li>Ensure <b>Template is ready</b> is selected.</li>
<li>Select <b>Upload a template file</b>.</li>
<li>Click on the <b>Choose file</b> button.</li>
<li>Select appropriate <b>CodePipelineS3.yaml</b> file.</li>
<li>Click on the <b>Next</b> button.</li>
<li>Enter the <b>Stack name</b> as "csi140-resource-stack".</li>
<li>Enter the <b>PipelineBucket</b> as "codepipeline-<aws::region>-<aws::accountid>".</li>
<li>Enter the <b>PipelineName</b> as "csi140".</li>
<li>Enter the <b>SourceBucket</b> as "cf-source-<aws::region>-<aws::accountid>".</li>
<li>Enter the <b>SourceKey</b> as the name of the file ythat was uploaded into "cf-source-<aws::region>-<aws::accountid>"</li>
<li>Enter the <b>YAMLdir</b> as "CloudFormation"</li>
<li>Click on the <b>Next</b> button.</li>
<li>Click on the <b>Next</b> button.</li>
<li>tick <b>I acknowledge that AWS CloudFormation might create IAM resources.</b>.</li>
<li>Click on the <b>Create stack</b> button.</li>
</ul>
This should create the "csi140-resource-stack", which when complete will create a CodePipeline. This can be found by now doing the following:
<ul>
<li>Go to CodePipeline Dashboard</li>
<li>Open <b>Pipeline - CodePipeline</b> and click on <b>Pipelines</b></li>
<li>Click on the "cis140-codepipeline".</li>
<li>It should be running, if not</li>
<ul>
<li>Click on the <b>Release change</b> button.</li>
</ul>
</ul>
Once complete, all resources will have been created and the Lambda schedule running. The associated CloudFormation Stacks can be viewed in CloudFormation.
