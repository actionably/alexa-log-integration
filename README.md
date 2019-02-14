# alexa-log-integration
Dashbot integration method for Alexa Skills via CloudWatch Logs.

## Requirements
  - AWS Account
  - AWS CLI
  - AWS Configuration
  - AWS Lambda serving your Alexa Skill
  
For more info on the AWS CLI, click [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html).

## Setup

### 1. Deploy CloudFormation Stack
Change to this directory and run the following in the command line (Make sure to replace 
the `<YOUR DASHBOT API KEY HERE>` and `<YOUR LOG GROUP HERE>` with your Dashbot API Key and 
a CloudWatch Logs Log Group name that is not already used.):

```bash
aws cloudformation deploy --template-file ./cloudformation/serverless.yml \
                          --stack-name testing-dashbot-log-integration-deployment \
                          --capabilities CAPABILITY_IAM \
                          --parameter-overrides \
                            NodeEnvironment=testing \
                            ExistingResourcesStack=testing-existing-resources \
                            DashbotApiKey=<YOUR DASHBOT API KEY HERE> \
                            LogGroup=<YOUR LOG GROUP HERE>
```
 
Take note of the `<YOUR LOG GROUP HERE>` you specified. You will need to refer to this
name later. The stack will fail to create if the CloudWatch Logs Log Group you specified
already exists.

### 2. Add Dashbot Library Configuration

In your lambda function that handles Alexa Skills requests, call the logIntegration method 
on the dashbot alexa object giving configuration options as specified by the 
[dashbot-logger](https://github.com/actionably/dashbot-logger) library. The minimum parameter
to set is the logGroupName that you have specified above when creating your CloudFormation stack.

```javascript
const dashbot = require('dashbot')('YOUR_DASHBOT_API_KEY').alexa.logIntegration({logGroupName: 'YOUR_LOG_GROUP_NAME'});
```

Replace the `YOUR_DASHBOT_API_KEY` and the `YOUR_LOG_GROUP_NAME` with your Dashbot API key and
the log group name you specified above.  