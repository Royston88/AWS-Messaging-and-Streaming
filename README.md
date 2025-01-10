# AWS-Messaging-and-Streaming
A Q&amp;A on some services related to messaging and streaming in AWS.

## 1. Does SNS guarantee exactly once delivery to subscribers?
No, SNS (Simple Notification Service) does **not** guarantee exactly once delivery to subscribers. SNS provides a **best-effort** delivery model, which means it attempts to deliver messages to subscribers but does not guarantee that a message will be delivered only once.

* **At least once delivery**: SNS guarantees that messages will be delivered at least once to the subscribers. In some cases, due to network issues or retries, the message may be delivered multiple times.
* **Possible duplicate messages**: If a subscriber fails to acknowledge the message or if there's an issue during delivery, SNS may resend the message. As such, it's important for subscribers to handle duplicate messages appropriately.

## 2. What is the purpose of the Dead-letter Queue (DLQ)?
The **Dead-letter Queue (DLQ)** is used to **store messages** that could not be successfully processed or delivered by the original queue or topic. The purpose of a DLQ is to isolate and capture failed messages so they don't block the normal processing flow.

* **For SQS**: In the context of **SQS (Simple Queue Service)**, a DLQ is used to capture messages that couldn't be processed after multiple retry attempts. Once a message exceeds the configured maximum number of receive attempts, it's moved to the DLQ.

* **For SNS**: In SNS, messages that fail to be delivered to subscribers (e.g., due to network issues or subscriber failures) can be routed to a DLQ for later analysis or retry.

* **For EventBridge**: Similarly, EventBridge allows failed events to be sent to a DLQ, which can be used for troubleshooting and reprocessing of those events.

## 3. How would you enable a notification to your email when messages are added to the DLQ?
To enable a notification to your email when messages are added to a **Dead-letter Queue (DLQ)**, you can use **CloudWatch** to monitor the DLQ and then set up an **SNS topic** to notify you via email.

### Create a CloudWatch Alarm:

* Go to the **CloudWatch console**.
* Create a new **Alarm** that monitors the DLQ. Specifically, you would want to monitor the "**NumberOfMessagesSent**" metric for the DLQ, which counts how many messages are sent to the DLQ.
* Set the threshold for the alarm based on the condition that would indicate a failure (e.g., when the number of messages sent to the DLQ is greater than zero).

### Create an SNS Topic:

* Go to the **SNS console**.
* Create a new **SNS topic** that will handle the email notification.
* Subscribe your email address to the SNS topic.

### Set the CloudWatch Alarm to Trigger the SNS Notification:

* In the CloudWatch Alarm configuration, set the alarm action to **send a notification to the SNS topic** you just created when the condition is met (e.g., when messages are added to the DLQ).

Once set up, the CloudWatch alarm will trigger and send an SNS notification to your email whenever messages are added to the DLQ.
