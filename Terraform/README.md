# Azure Infrastructure Automation with Terraform

## Project Background
You and your team are working on a project in Azure. 
At the very beginning, you just created resources by clicking in the Portal, but as your project grew, you found it problematic to remember all the steps needed every time.
You decided to start from scratch and use an automation tool so that you can easily create multiple environments, or recreate them if something bad happens.

You and your team have decided to go with Terraform. The first elements that need to be created are the storage account (with container for blobs) and Service Bus queue. These elements are connected together because the queue should be notified when someone uploads any file to the storage bucket.

A small VM will also be required with 1 attached disk. Over time the number of these vm's, size and number of disks will increase. 

## Implementation Requirements
You have prepared the requirements and now you're ready to implement them in Terraform:

- Define a storage account named `upload_storage_account` in Terraform and `uploadstorageaccount` in Azure. The account tier should be Standard and replication should be LRS.
- Define a storage container in the storage account created in step 1. It should be named `upload_container` in Terraform and `upload-container` in Azure. The container access type should be blob.
- Define a Service Bus namespace named `upload_queue_ns` in Terraform and `upload-queue-ns` in Azure. The SKU should be set to Standard.
- Define a Service Bus queue in the namespace created in step 3. It should be named `upload_queue` in Terraform and `upload-queue` in Azure. Partitioning should be enabled for this queue.
- Create an Event Grid event subscription to connect everything together. It should be named `upload_subscription` in Terraform and `upload-event-subscription` in Azure:
  - It should use the `upload_container` created in step 2 as its scope.
  - It should reference the `upload_queue` created in step 4 as its Service Bus queue endpoint.
  - It should be triggered on a `Microsoft.Storage.BlobCreated` event.
- The Virtual Machine is not dependent on either the storage account or service bus but does need to be deployed alongside these other resources.

## Show & Tell

##### Walk us through the code detailing all the decisions you made along the way for