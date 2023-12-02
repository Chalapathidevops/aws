# AWS CloudFormation

AWS CloudFormation is a service provided by Amazon Web Services (AWS) that helps you create and manage your cloud resources in a structured and repeatable way using templates. Think of CloudFormation as a tool that allows you to describe the infrastructure you want (like servers, databases, networking configurations, etc.) using a simple text file called a template.

* **Templates:** These are files written in JSON or YAML format that describe the AWS resources you want to create. They contain configuration details like the type of resources, their properties, relationships between them, and any other necessary settings.
* **Resource Provisioning:** Once you have your template ready, you can use CloudFormation to launch and manage the resources specified in the template. CloudFormation takes care of the underlying provisioning process, ensuring that the resources are created in the right order and with the correct configurations.
* **Automation and Consistency:** CloudFormation allows you to automate the deployment and management of your infrastructure. This helps maintain consistency across your environments (like development, testing, production) because you use the same template to create them all. If changes are needed, you modify the template, and CloudFormation handles the updates accordingly.
* **Stacks:** When you deploy a CloudFormation template, it creates a 'stack.' A stack is a collection of AWS resources that are created and managed together as a single unit. You can create, update, or delete entire stacks and their associated resources in a controlled and organized manner.
* **Change Management:** CloudFormation also helps in managing changes to your infrastructure. It keeps track of changes made to your resources and allows you to update your existing stacks with these changes without affecting the running applications.

## Sample template for creating S3 bucket

```
Resources:
  S3Bucket:
    Type: 'AWS::S3::Bucket'
    Properties: 
      BucketName: 'lovely-test-2'
      VersioningConfiguration: 
        Status: 'Enabled'
```
