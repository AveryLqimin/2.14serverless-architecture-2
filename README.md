1. Does SNS guarantee exactly-once delivery to subscribers?

No, Amazon SNS does not guarantee exactly-once delivery.

Standard SNS provides at-least-once delivery: a message is delivered to a subscriber at least once, but in rare cases, it might be delivered more than once (duplicates).

FIFO SNS (First-In-First-Out) introduced later does guarantee exactly-once message ordering and deduplication (if configured properly with a MessageDeduplicationId).

So the guarantee depends on whether you’re using Standard SNS (at-least-once) or FIFO SNS (exactly-once with ordering).

2. What is the purpose of the Dead-letter Queue (DLQ)?

A Dead-letter Queue (DLQ) is a place where messages that can’t be successfully delivered or processed end up.

For SQS, a DLQ stores messages that a consumer failed to process after the configured number of retries.

For SNS, a DLQ stores messages that SNS itself couldn’t deliver to a subscribed endpoint (like Lambda, SQS, or HTTP/S).

For EventBridge, a DLQ stores events that couldn’t be delivered to the target after retries.

The main purpose:
Prevent message loss
Allow debugging and recovery (you can inspect failed messages)
Decouple error handling from the main flow

3. How would you enable a notification to your email when messages are added to the DLQ?

You can set this up using SNS + CloudWatch Alarms (or EventBridge). Example flow:

Enable DLQ for your SQS/SNS/EventBridge resource.

Suppose it’s an SQS DLQ.

Publish CloudWatch metrics:

SQS publishes metrics like ApproximateNumberOfMessagesVisible.

You can create a CloudWatch Alarm when ApproximateNumberOfMessagesVisible > 0 in your DLQ.

Send Alarm to SNS Topic:

Configure the alarm to send a notification to an SNS topic.

Subscribe your Email to SNS Topic:

Add your email as a subscriber to that topic.

Confirm the subscription via the email confirmation link.

Result: Whenever a message lands in the DLQ, the CloudWatch Alarm triggers, SNS sends an email, and you’re notified.
