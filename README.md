# Integrating physical devices with IOTA — Amazon Alexa

## The 20th part in a series of beginner tutorials on integrating physical devices with the [IOTA protocol](https://blog.coincodecap.com/iota-coin-explained-for-beginners)

![Image for post](https://miro.medium.com/max/2197/1*0Mare4fWGFLmIUbbMsiAYA.png)

*Alexa, Please send fifty IOTA’s to Alice*
*Alexa, What is my IOTA balance?*
*Alexa, What is the current IOTA transaction throughput?*

These are some of the cool things you could teach Alexa to do after completing this tutorial…

## Introduction

This is the 20th part in a series of beginner tutorials where we explore integrating physical devices with the IOTA protocol. In this tutorial we will look at integrating the [Amazon Alexa](https://en.wikipedia.org/wiki/Amazon_Alexa) virtual assistant with the IOTA protocol. At the same time, we will also get an introduction to the concept of cloud computing as our Python code for this project will be hosted as an [AWS Lambda function](https://aws.amazon.com/lambda/).

------

## The Use Case

Over time our hotel owner have ended up with multiple IOTA addresses where he receives funds from various IOTA powered devices and services. Now and then he needs to check the balance for these addresses. Only problem is that opening his Trinity wallet or checking [thetangle.org](https://thetangle.org/) takes him away from other tasks he might be doing at the same time. What if he could use some type of voice activated assistant to help him out? He could then get the information he needs, while at the same time stay focused on the task at hand.

Let’s see if we can help him out..

------

## What is Amazon Alexa?

Amazon Alexa is a virtual voice assistant app. developed by Amazon. The Alexa app. comes embedded in many different Alexa enabled devices such as smart speakers, smart TV’s, smart HI-FI systems, smart watches etc. You may even have Alexa embedded in your car in case you haven’t noticed.

![Image for post](https://miro.medium.com/max/810/0*KGtb6b2Lwr3HTUU1.jpg)

*Note!*
*Notice that you don’t need an Alexa enabled device to talk to Alexa. You can simply install the Amazon Alexa app. on your IOS or Android smartphone.*

------

## What are Alexa Skills?

Although Alexa can do a lot of things out-of-the box.., some times we need to teach her new “skills” that she does not already know how to do. And this is exactly what we will do in this tutorial.

At their simplest, Alexa skills are like voice activated smartphone apps. They are created by third party developers (like us). Some Alexa Skills work entirely on their own, while others interact with something else, such as an online service like Spotify, a smart home product like a Roomba robotic vacuum cleaner, or as in our case, the IOTA tangle.

------

## What is AWS Lambda?

AWS Lambda is a an event-driven, serverless computing platform provided by Amazon as a part of Amazon Web Services (AWS). AWS Lambda allows us to host and run the Python code required by our new Alexa Skill. The Python code (with all its dependencies/libraries) is typically packaged as a .zip file and uploaded to AWS in the form of an AWS Lambda function. We can now trigger the Lambda function (Python code) based on the response from some other AWS service, such as Alexa.

You can create and host AWS Lambda functions in the [AWS Management Console](https://aws.amazon.com/console/) for free by signing up for an AWS free tier account [here](https://aws.amazon.com/account/)

*Note!*
*AWS Lambda functions can be written different programming languages, such as Node.js, Java and Go, in case you prefer to write your Lambda code in one of these languages. In my example, I’m decided to go with Python as you may already be familiar with the PyOTA library from my previous tutorials.*

------

## Step-by-step implementation

When developing a new Alexa Skill we typically start by creating a new *Alexa Skill* in the [*Alexa Developer Console*](https://developer.amazon.com/alexa/console/ask). Next, we define how we will interact with Alexa so that she understands our intent during the conversation. Next, we take the Python code where we have all the functions needed for our new Alexa Skill and upload the code as a hosted *AWS Lambda function* in the Amazon Web Services cloud. Next, we “connect” our Alexa Skill to the Lambda function so that Alexa know what code and functions to run when we interact with her. And finally, we test our new *Alexa Skill*.

Let’s take them step-by-step..

### Step 1 — Create a new Alexa Skill

Let’s start by creating a new Alexa Skill in the Alexa Developer Console

To create a new Alexa skill you first need to sign up for a free Alexa developer account. You will find a link to the Alexa Developer Console [here](https://developer.amazon.com/alexa/console/ask).

After signing up and loging in you should now see the main page of the Alexa Developer Console.

Lets start by creating a new Alexa skill using the “Create Skill” button.

![Image for post](https://miro.medium.com/max/985/1*S3Wfj7qufuUPicBRO8IYGg.png)

Next, we need to give our new skill a name, a default language, a model and a hosting method.

Let’s call the new skill **iota_balance** with default language **English (US)**. As model, select **Custom**, and as hosting method, select **Provision your own** before pushing the “Create Skill” button

Next, we need to select a template for our new skill.

Let’s select the **Hello World Skill** template before pushing the “Continue with template” button.

### Step 2 — Specify invocation, intents and utterances

Next, we need to teach Alexa how we will invoke and interact with her new skill during a typical conversation. This is done using *Invocations*, *Intents* and *Utterances*.

**Invocation**
The *Invocation* let’s Alexa know what skill to perform when we call for her. In my example i decided to go with the phrase: **get iota balance** So if I now want Alexa to perform this particular skill, i would simply say: **Alexa, get iota balance**

![Image for post](https://miro.medium.com/max/550/1*Mm29c9yESctj0ws2TLIMUg.png)

**Intents**
The *Intents* section specifies various “functions” within Alexa’s new skill that i might want to invoke during our conversation. In my example I have two main intents:

1. Get the balance of my personal IOTA address
2. Get the balance of the Hotel IOTA address

In my example I named these intents: *PersonalBalance* and *HotelBalance*

![Image for post](https://miro.medium.com/max/411/1*fGceyvOfr68EKi6x7H1IPw.png)

*Note!
Notice that there are some Buildt-In Intents (CancelIntent, HelpIntent etc.) that came with the* **Hello World Skill** *template. These are typical intents you would invoke in case you want to exit the skill, get some more information about the skill etc. For now, just leave them within the list of Intents.*

**Utterances**
For each Intent you also need to specify some typical phrases you would use to call a particular Intent. These phrases are called *Utterances* and are associated with there respective Intent. For instance, if I want to invoke the *PersonalBalance* Intent, I might say something like “Get my personal balance” or “What is my personal balance”.

![Image for post](https://miro.medium.com/max/767/1*49qX-O-BoL__2K4BGh97Ag.png)

*Note!*
*By using some clever machine learning algorithms, Alexa normally understands your intent even if you don’t say the sample utterances directly as spelled out. It is often enough to just include one or two key words in the sentence.*

------

### Step 3 — Creating and uploading the AWS Lambda function for our Alexa Skill

Next, we will create a new AWS lambda function in AWS and upload the Python code to be used by our new Alexa Skill.

Start by going to the [AWS Management Console](https://aws.amazon.com/console/) and find the Lambda service.

After selecting the Lambda service, select functions and push the “Create function button”

Select the “Author from Scratch” template and give your new Lambda function a name. In my example I used the name *iota_balance*. Finally, select *Python 3.8* as your programming language and press the “Create function” button.

When the new Lambda function opens, next thing we need to do is to specify that the function will be triggered by an Alexa Skill. Go to the *Designer* panel and push the “Add trigger” button. Then from the “Trigger configuration” list, select “Alexa Skills Kit”. When asked for your *Skill ID*, paste the Skill ID for the Alexa Skill created in Step 1.

*Note!*
*You will find the Skill ID for your Alexa Skill by selecting Endpoint in your Alexa Skill.*

![Image for post](https://miro.medium.com/max/1557/1*fJNwb0xC5ZvLSLSqmtj5sw.png)

Now that we have created our new Lambda function we can start uploading the Python code that will be associated with the function.

As mentioned previously, when hosting Python code as a Lambda function it is important that we also include and upload any non-standard Python libraries required by our function. In our case, the PyOTA library is required so we have to make sure we also include the files from this library. The way we do this in practice is that we package all our required files in one .zip file before uploading the .zip file to the AWS Lambda function.

*Important!
I have prepared a .zip file you can use in your Lambda function to give you a head start while creating your first Alexa Skill. The .zip file includes the lambda_function.py file used by Alexa together with the PyOTA library required when interacting with the IOTA tangle. You can find and download the .zip file (get_iota_balance.zip) in this* [*Github repository*](https://github.com/huggre/amazon_alexa)*.*

Now that we have our .zip file, go to your new Lambda function in the AWS Management Console, select the *Actions* drop-down menu in the *Function code* panel and then select “Upload a .zip file”. You may now select and upload your .zip file.

### Step 4 — Connecting your Alexa Skill with the Lambda function

Last thing we need to do before we can start testing our new Alexa Skill is to “connect” our Alexa Skill to the Lambda function so that Alexa knows what Lambda function to use when performing her new Skill.

Start by copying the ARN id you see in the upper right corner when inside the Lambda function

![Image for post](https://miro.medium.com/max/662/1*oRLQBZBDNdI7CObhraugpQ.png)

Next, go to your Alexa Skill, select *Endpoint* and paste the id in the *Default Region* field

![Image for post](https://miro.medium.com/max/811/1*aMPA0lovOkNTRX9UJPMN7Q.png)

That’s it.., hit the “Save Model” and “Build Model” buttons in your Alexa Skill and we can start testing our new Alexa Skill.

### Step 4 — Testing our Alexa Skill

To test our new Alexa Skill, select “Test” from the Alexa Skill menu and then select *Development* from the “Skill testing is enabled in:” drop-down menu.

You can now start typing text or talking to Alexa by pushing the microphone icon in the Alexa Simulator.

If everything is working correctly you should see (and hear) the dialog going something like this:

![Image for post](https://miro.medium.com/max/441/1*P6Kqn0Zf3lh2xGTKGe3pRQ.png)

------

## The Python Code

Let’s have a quick look at the Lambda function (Python code) for this project and how it works.

When looking at the code you will see that we have some standard functions required for interacting with Alexa. The most important being the *on_intent()* function. Here we define the code to be executed as we call for each individual Intent as described in Step 2.

So in my example, if you say something like “Get my personal balance” or “What is my personal balance”, the Alexa Skill knows that the Intent we want to execute is the *PersonalBalance* intent. Next, we simply call a new custom function (*get_personal_balance())* where we have all the code for interacting with the IOTA tangle together with the returning responses from Alexa.

That’s it, and here is the code..

```python
"""
This is a Python template for Alexa to get you building skills (conversations) quickly.
"""

from __future__ import print_function

from iota import Iota

api = Iota('https://nodes.devnet.iota.org:443')


# --------------- Helpers that build all of the responses ----------------------

def build_speechlet_response(title, output, reprompt_text, should_end_session):
    return {
        'outputSpeech': {
            'type': 'PlainText',
            'text': output
        },
        'card': {
            'type': 'Simple',
            'title': "SessionSpeechlet - " + title,
            'content': "SessionSpeechlet - " + output
        },
        'reprompt': {
            'outputSpeech': {
                'type': 'PlainText',
                'text': reprompt_text
            }
        },
        'shouldEndSession': should_end_session
    }

def build_response(session_attributes, speechlet_response):
    return {
        'version': '1.0',
        'sessionAttributes': session_attributes,
        'response': speechlet_response
    }


# --------------- Functions that control the skill's behavior ------------------


def get_balance(address):
    gb_result = api.get_balances([address])
    balance = gb_result['balances']
    return (balance[0])


def get_personal_balance():
    """ An example of a custom intent. Same structure as welcome message, just make sure to add this intent
    in your alexa skill in order for it to work.
    """
    # Insert your personal IOTA address here
    address = "XUGOERAHKXQCVPPVVIPIJGLUTLTKFHYGMBBLOXJFYGSARLOTYFFSDZNYCOBOCNPGRMJWZCQBNOROUCE9G"
    balance = get_balance(address)
    session_attributes = {}
    card_title = "Pesonal Balance"
    speech_output = "Your personal balance is " + str(balance) + " iota"
    reprompt_text = "You never responded to the first test message. Sending another one."
    should_end_session = False
    return build_response(session_attributes, build_speechlet_response(
        card_title, speech_output, reprompt_text, should_end_session))

def get_hotel_balance():
    """ An example of a custom intent. Same structure as welcome message, just make sure to add this intent
    in your alexa skill in order for it to work.
    """
    # Insert the Hotel IOTA address here...
    address = 'YOURADDRESSFROMTHEPREVIOUSTUTORIAL'
    balance = get_balance(address)
    session_attributes = {}
    card_title = "Hotel Balance"
    speech_output = "Your hotel balance is " + str(balance) + " iota"
    reprompt_text = "You never responded to the first test message. Sending another one."
    should_end_session = False
    return build_response(session_attributes, build_speechlet_response(
        card_title, speech_output, reprompt_text, should_end_session))

def get_welcome_response():
    """ If we wanted to initialize the session to have some attributes we could
    add those here
    """
    session_attributes = {}
    card_title = "Welcome"
    speech_output = "Welcome to the iota balance application, what can i do for you?"
    # If the user either does not reply to the welcome message or says something
    # that is not understood, they will be prompted again with this text.
    reprompt_text = "I don't know if you heard me, welcome to the iota balance application!"
    should_end_session = False
    return build_response(session_attributes, build_speechlet_response(
        card_title, speech_output, reprompt_text, should_end_session))


def handle_session_end_request():
    card_title = "Session Ended"
    speech_output = "Thank you for using the iota balance application. " \
                    "Have a nice day! "
    # Setting this to true ends the session and exits the skill.
    should_end_session = True
    return build_response({}, build_speechlet_response(
        card_title, speech_output, None, should_end_session))

# --------------- Events ------------------

def on_session_started(session_started_request, session):
    """ Called when the session starts.
        One possible use of this function is to initialize specific 
        variables from a previous state stored in an external database
    """
    # Add additional code here as needed
    pass

    

def on_launch(launch_request, session):
    """ Called when the user launches the skill without specifying what they
    want
    """
    # Dispatch to your skill's launch message
    return get_welcome_response()


def on_intent(intent_request, session):
    """ Called when the user specifies an intent for this skill """

    intent = intent_request['intent']
    intent_name = intent_request['intent']['name']

    # Dispatch to your skill's intent handlers
    if intent_name == "PersonalBalance":
        return get_personal_balance()
    elif intent_name == "HotelBalance":
        return get_hotel_balance()
    elif intent_name == "AMAZON.HelpIntent":
        return get_welcome_response()
    elif intent_name == "AMAZON.CancelIntent" or intent_name == "AMAZON.StopIntent":
        return handle_session_end_request()
    else:
        raise ValueError("Invalid intent")


def on_session_ended(session_ended_request, session):
    """ Called when the user ends the session.
    Is not called when the skill returns should_end_session=true
    """
    print("on_session_ended requestId=" + session_ended_request['requestId'] +
          ", sessionId=" + session['sessionId'])
    # add cleanup logic here


# --------------- Main handler ------------------

def lambda_handler(event, context):
    """ Route the incoming request based on type (LaunchRequest, IntentRequest,
    etc.) The JSON body of the request is provided in the event parameter.
    """
    print("Incoming request...")

    """
    Uncomment this if statement and populate with your skill's application ID to
    prevent someone else from configuring a skill that sends requests to this
    function.
    """
    # if (event['session']['application']['applicationId'] !=
    #         "amzn1.echo-sdk-ams.app.[unique-value-here]"):
    #     raise ValueError("Invalid Application ID")

    if event['session']['new']:
        on_session_started({'requestId': event['request']['requestId']},
                           event['session'])

    if event['request']['type'] == "LaunchRequest":
        return on_launch(event['request'], event['session'])
    elif event['request']['type'] == "IntentRequest":
        return on_intent(event['request'], event['session'])
    elif event['request']['type'] == "SessionEndedRequest":
        return on_session_ended(event['request'], event['session'])
```

------

## Creating your version of the project

The simplest way to get you started with creating your own version of this project (such as changing the IOTA addresses used for checking balances) is to simply modify the *lambda_function.py* file inside the .zip file and upload the modified .zip file to your existing Lambda function.

------

## Contributions

If you would like to make any contributions to this tutorial you will find a Github repository [here](https://github.com/huggre/amazon_alexa)

------

## Donations

If you like this tutorial and want me to continue making others feel free to make a small donation to the IOTA address below.

![Image for post](https://miro.medium.com/max/382/0*JyR_ttsrPttUHye6.png)

NYZBHOVSMDWWABXSACAJTTWJOQRPVVAWLBSFQVSJSWWBJJLLSQKNZFC9XCRPQSVFQZPBJCJRANNPVMMEZQJRQSVVGZ
