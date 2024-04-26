# Esse projeto consiste em demonstrar o poder da plataforma azure language studio.

## Analise pessoal
    a plataforma azure nos dá insumos suficientes para o deploy de uma solução costumizada e consegue também elevar o valor agragado do projeto

## PRINTS
    [Alt text](./TexttoSentimentRecognition/input/Screenshot_2.jpg?raw=true "Title")
    [Alt text](./TexttoSentimentRecognition/input/Screenshot_1.jpg?raw=true "Title")
    [Alt text](./TexttoSentimentRecognition/input/Screenshot_3.jpg?raw=true "Title")
    [Alt text](./TexttoSentimentRecognition/input/Screenshot_4.jpg?raw=true "Title")
    
    
## Configuração de ambiente
    Create an Azure resource
To use the code sample below, you'll need to deploy an Azure resource. This resource will contain a key and endpoint you'll use to authenticate the API calls you send to the Language service.

Use the following link to create a language resource using the Azure portal. You will need to sign in using your Azure subscription.

On the Select additional features screen that appears, select Continue to create your resource.

A screenshot showing additional feature options in the Azure portal.

In the Create language screen, provide the following information:

Detail	Description
Subscription	The subscription account that your resource will be associated with. Select your Azure subscription from the drop-down menu.
Resource group	A resource group is a container that stores the resources you create. Select Create new to create a new resource group.
Region	The location of your Language resource. Different regions may introduce latency depending on your physical location, but have no impact on the runtime availability of your resource. For this quickstart, either select an available region near you, or choose East US.
Name	The name for your Language resource. This name will also be used to create an endpoint URL that your applications will use to send API requests.
Pricing tier	The pricing tier for your Language resource. You can use the Free F0 tier to try the service and upgrade later to a paid tier for production.
A screenshot showing resource creation details in the Azure portal.

Make sure the Responsible AI Notice checkbox is checked.

Select Review + Create at the bottom of the page.

In the screen that appears, make sure the validation has passed, and that you entered your information correctly. Then select Create.

Get your key and endpoint
Next you will need the key and endpoint from the resource to connect your application to the API. You'll paste your key and endpoint into the code later in the quickstart.

After the Language resource deploys successfully, click the Go to Resource button under Next Steps.

A screenshot showing the next steps after a resource has deployed.

On the screen for your resource, select Keys and endpoint on the left navigation menu. You will use one of your keys and your endpoint in the steps below.

A screenshot showing the keys and endpoint section for a resource.

Create environment variables
Your application must be authenticated to send API requests. For production, use a secure way of storing and accessing your credentials. In this example, you will write your credentials to environment variables on the local machine running the application.

 Tip

Don't include the key directly in your code, and never post it publicly. See the Azure AI services security article for more authentication options like Azure Key Vault.

To set the environment variable for your Language resource key, open a console window, and follow the instructions for your operating system and development environment.

To set the LANGUAGE_KEY environment variable, replace your-key with one of the keys for your resource.
To set the LANGUAGE_ENDPOINT environment variable, replace your-endpoint with the endpoint for your resource.
Windows
Linux
macOS
Console

Copy
setx LANGUAGE_KEY your-key
Console

Copy
setx LANGUAGE_ENDPOINT your-endpoint
 Note

If you only need to access the environment variables in the current running console, you can set the environment variable with set instead of setx.

After you add the environment variables, you may need to restart any running programs that will need to read the environment variables, including the console window. For example, if you are using Visual Studio as your editor, restart Visual Studio before running the example.

Install the client library
After installing Python, you can install the client library with:

Console

Copy
pip install azure-ai-textanalytics==5.2.0
Code example
Create a new Python file and copy the below code. Then run the code

Python

Copy
# This example requires environment variables named "LANGUAGE_KEY" and "LANGUAGE_ENDPOINT"
language_key = os.environ.get('LANGUAGE_KEY')
language_endpoint = os.environ.get('LANGUAGE_ENDPOINT')

from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

# Authenticate the client using your key and endpoint 
def authenticate_client():
    ta_credential = AzureKeyCredential(language_key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=language_endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()

# Example method for detecting sentiment and opinions in text 
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable. The concierge was nice, however."
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
Output
Console

Copy
Document Sentiment: mixed
Overall scores: positive=0.47; neutral=0.00; negative=0.52

Sentence: The food and service were unacceptable.
Sentence sentiment: negative
Sentence score:
Positive=0.00
Neutral=0.00
Negative=0.99

......'negative' target 'food'
......Target score:
......Positive=0.00
......Negative=1.00

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.00
......Negative=1.00

......'negative' target 'service'
......Target score:
......Positive=0.00
......Negative=1.00

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.00
......Negative=1.00



Sentence: The concierge was nice, however.
Sentence sentiment: positive
Sentence score:
Positive=0.94
Neutral=0.01
Negative=0.05

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00
Clean up resources
If you want to clean up and remove an Azure AI services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

Portal
Azure CLI
Use the following commands to delete the environment variables you created for this quickstart.

Windows
Linux
macOS
Console

Copy
reg delete "HKCU\Environment" /v LANGUAGE_KEY /f
Console

Copy
reg delete "HKCU\Environment" /v LANGUAGE_ENDPOINT /f


 
