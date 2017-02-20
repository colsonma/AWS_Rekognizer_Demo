# AWS_Rekognizer_Demo
First thanks to Ben Snively and Rob Nolen for all their help with this. This is a quick demo of AWS rekognizer a photo analytics and recognition service, tied to a slackbot. A photo upload to slack triggers rekognizer to post in Slack about what the image is or what's recognized. In turn there is an S3 copy of the image which then triggers a lambda function to check the photo against a watchlist bucket. If the photo is matched this queues an MQTT thread to be posted to a thing shadow in AWS IoT which is connected to a Phillips Hue Controller managing a Hue Go light. It turns the light from Green to Red signaling that the person is on a watchlist. In order to clear that alert an Amazon Dot is used with a custom Alexa skill to trigger a Lambda function that posts a clear message back to the MQTT thread. There will be future iterations of this so stay tuned

## Setup instructions

Pre-requisits:
 - AWS IAM Account with the following policies enabled
    - IAMSReadOnlyAccess
    - AWSS3FullAccess
    - AWSLambdaFullAccess
    - AWSIoTFullAccess
    - AWSCloudFormationReadOnlyAccess
    - AWSAPIGatewayAdministrator
    - AmazonRekognitionFullAccess

- EC2 AWS Linux instances for build
    - python
    - pip 
    - AWSCLI tools
    - AWSCLI is configured with IAM account above
    
- Gear
    - Philips Hue Controller
    - Philips Hue Go light
    - Wireless Router
    - Amazon Dot or Echo
    
- Local Laptop Setup
    - Must have Node.JS installed locally
    - Admin priviledges
    - Slack Client

1. Visit https://api.slack.com/apps and click __Create New App__. Name
   your app and click __Create App__.

1. Navigate to __Bot Users__. Click __Add a Bot User__. Confirm your
   bot's name and click __Add Bot User__.

1. Navigate to __Event Subscriptions__.

   * Next to __Enable Events__, toggle the switch to __On__.

   * Scroll down to __Bot Events__. Click __Add Bot User Event__ and
     choose `message.channels`.

   * Click __Save Changes__.

1. Navigate to __Basic Information__ and note your app ID, app secret,
   and verification token.

1. Clone this repository onto a Linux host with `pip` and the AWS CLI
   installed.

1. Run `bin/build` to build and upload the Lambda artifacts.

1. Run `bin/launch` to deploy the CloudFormation template. To complete
   installation:

   * Navigate to __OAuth &amp; Permissions__. Paste the OAuth callback
     URL in the __Redirect URL(s)__ box and click __Save Changes__.

   * Navigate to __Event Subscriptions__. Paste the event target URL
     in the __Request URL__ box and click __Verify__.

   * Next to __Enable Events__, toggle the switch to __On__.

   * Click __Save Changes__.

   * Paste the OAuth link into your web browser and complete the OAuth flow.
   
Rekognizer Steps: next steps
    * Turn on S3 events for the faces prefix have it invoke the lambda function that compares it to the rekognition collection
    ** create a rekognition collection via the cli and add some folks to the watch list the lambda functino will query that collectionid based on the faces that get cropped

IoT Steps:
  * Create a Thing in AWS IoT using the Wizard this will be your Hue Connection Bridge
  * Download and save the Root.CA and Device Private and Public Keys, along with the SDK
  * Complete the Scripts this currently runs locally

