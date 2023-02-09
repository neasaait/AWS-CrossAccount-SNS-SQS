# SQS subscribe to cross account SNS

## Youtube video link
[![AWS-CrossAccount-SNS-SQS](https://img.youtube.com/vi/videoId/0.jpg)](https://www.youtube.com/watch?v=videoId)

## Use case
Assume we have 2 AWS accounts ***Account-A*** (112233445566) and ***Account-B*** (223344556677)

SNS topic ***MyTestTopic*** own by ***Account-A***.
SQS ***MyTestQueue*** own by ***Account-B***.

In this example we want SQS ***MyTestQueue*** subscribe to SNS topic ***MyTestTopic***.

This demo is using root user of ***Account-A*** and ***Account-B***.
> :no_entry_sign: Note: root user is used only for demo, should not be use for development/production.

#### :point_right: Step 1: Create SNS topic and SQS queue

Login to ***Account-A*** and create a SNS topic ***MyTestTopic*** with default properties.
Sample SNS topic ARN: arn:aws:sns:us-east-1:112233445566:***MyTestTopic***


Login to ***Account-B*** and create a SSQS ***MyTestQueue*** with default properties.
SAmple SQS queue ARN: arn:aws:sqs:us-east-1:223344556677:***MyTestQueue***


#### :point_right: Step 2: Update SNS policy to allow subscription to other account SQS
In this step, we are updating SNS topic policy to allow other account SQS to subscribe this topic.
Login to ***Account-A*** and update SNS topic ***MyTestTopic*** policy to include following statement:
```
{
      "Sid": "OtherAccountPermission",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::223344556677:root" 
      },
      "Action": "SNS:Subscribe",
      "Resource": "arn:aws:sns:us-east-1:112233445566:***MyTestTopic***" 
}
```
:warning: Note: Principal includes account id of SQS queue, and Resource is SNS topic ARN.

#### :point_right: Step 3: Subscribe to SNS topic <br/>
In step 2, we setup permission for SQS to subscribe. In this step, we are actually subscribing the SQS to SNS topic.

Login to ***Account-B*** account -> SQS -> Select Queue ***MyTestQueue*** -> Subscribe to Amazon SNS topic.<br/>
Enter SNS topic ARN e.g.: arn:aws:sns:us-east-1:112233445566:MyTestTopic <br/>
Watch [youtube video](https://www.youtube.com/watch?v=videoId) for details.

#### :point_right: Step 4: Test subscription
Login to ***Account-A*** -> SNS topic -> ***MyTestTopic*** ->  Publish Message <br/>
Enter subject and message and publish message to SNS.

Login to ***Account-B*** -> SQS -> ***MyTestQueue*** -> Send ad Receive messages -> Poll for messages<br/>
You should see the message in queue.
<br/><br/>


## References
* [AWS Reference doc](https://docs.aws.amazon.com/sns/latest/dg/sns-send-message-to-sqs-cross-account.html)
* [AWS blog](https://aws.amazon.com/blogs/compute/cross-account-integration-with-amazon-sns/)


