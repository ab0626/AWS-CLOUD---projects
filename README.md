# AWS Banker Chatbot Documentation

![image](https://github.com/user-attachments/assets/e5969f34-d8f8-4469-966f-af6234c08310)

Welcome to the **AWS Banker Chatbot** project! This chatbot is designed to assist users with common banking inquiries using **Amazon Lex** for natural language processing and interaction.

## 📦 Project Overview
The **Banker Chatbot** uses Amazon Lex to process and respond to user queries. It handles basic intents like balance inquiries, transaction history, and account management while providing fallback handling for unrecognized inputs.

## 📚 Key Features
### 🎯 Define a Basic Intent
- **Intent Definition:** The chatbot is configured with a basic intent in Amazon Lex to control how it responds to user inputs.
- **Example Intents:**
   - CheckAccountBalance
   - ViewTransactionHistory
   - TransferFunds

### 📝 Create Lists of Utterances
- **Utterance Collection:** The chatbot uses a variety of sample utterances to map user inputs to different intents.
- **Examples:**
   - "What's my balance?"
   - "Show me my recent transactions."
   - "I want to transfer money."

### 🛠️ Handle Failures with FallbackIntent
- **Fallback Management:** The `FallbackIntent` is configured to handle unrecognized inputs gracefully.
- **Example Response:**
   - "I'm not sure I understand. Could you please rephrase that?"

### 🔁 Define Variations for Responses
- **Message Variations:** A `MessageGroup` is implemented to offer semi-random responses, making the bot more conversational.
- **Example Responses:**
   - "Sure! Your current balance is $1,000."
   - "You have $1,000 in your account."

### 🧪 Build and Test Your Bot
- **Testing:** The chatbot can be tested in both text and voice formats within the Amazon Lex console.
- **Test Scenarios:**
   - Correct intent recognition.
   - Fallback handling.
   - Response variation consistency.

## 🎯 Project Goals
- **Improve customer experience** with automated banking assistance.
- **Demonstrate NLP capabilities** using Amazon Lex.
- **Ensure conversational flow** with message variation and fallback handling.

## 📖 How to Deploy
1. **Create a Lex Bot:** Log into the AWS Console and create a new Amazon Lex bot.
2. **Define Intents and Utterances:** Configure the intents and provide sample utterances.
3. **Configure Responses:** Add message variations and fallback responses.
4. **Enable Testing:** Test in both voice and text modes.
5. **Integrate with Lambda (Optional):** For dynamic responses, you can link the bot with an AWS Lambda function.

## 🚀 Next Steps
- Expand the intent library for more advanced banking operations.
- Implement Lambda functions for dynamic data fetching.
- Improve security with IAM roles and data encryption.

# AWS Banker Chatbot Technical Documentation (Part 2: Custom Slots)

This document focuses on creating and configuring **custom slots** for the AWS Banker Chatbot using Amazon Lex.

## 📦 Overview
Custom slots enable the chatbot to capture and process user inputs with specific data types, such as account types, transaction categories, and currencies.

## 🛠️ Creating Custom Slots
### Step 1: Access the Lex Console
- Log into the AWS Console and navigate to Amazon Lex.
- Select your existing **Banker Chatbot**.

### Step 2: Define Custom Slots
- Go to the **Slots** section under the chatbot's intent.
- Click **Create Slot Type**.
- Provide a **Name** and **Description**.

### Step 3: Configure Slot Values
- Add slot values to define the options the chatbot can recognize.
- Example for Account Type Slot:
  - "Savings"
  - "Checking"
  - "Business"

### Step 4: Assign Slots to Intents
- Navigate to the desired intent.
- Add a new slot by selecting the previously created slot type.
- Define the **Prompt Message** to request user input.

### Step 5: Test the Slots
- Test by asking questions like:
   - "What is my balance in my savings account?"
   - "Show me transactions from my business account."

## 🎯 Best Practices
- **Validation:** Use Lambda functions for complex data validation.
- **Synonyms:** Add synonyms for better user understanding.
- **Error Handling:** Implement fallback prompts for unrecognized inputs.

---
This completes the technical setup for custom slots in the AWS Banker Chatbot. ✅

# AWS Banker Chatbot Technical Documentation (Part 3: Connecting Lambda)

This document outlines the steps to connect an AWS Lambda function to the **AWS Banker Chatbot** for handling dynamic responses and backend processing.

## 📦 Overview
AWS Lambda allows you to add serverless computation to your Lex chatbot, enabling dynamic data retrieval and response generation.

## 🛠️ Creating and Configuring the Lambda Function
### Step 1: Create a Lambda Function
- Go to the **AWS Lambda Console**.
- Click **Create Function**.
- Choose **Author from Scratch**.
- Name your function, select **Python 3.x** as the runtime, and create it.

### Step 2: Add Lex Trigger
- Under **Function Overview**, click **Add Trigger**.
- Select **Lex**, choose your **Banker Chatbot**, and save the trigger.

### Step 3: Write Lambda Code
```python
import json

def lambda_handler(event, context):
    intent_name = event['currentIntent']['name']
    if intent_name == "CheckBalance":
        return {
            "dialogAction": {
                "type": "Close",
                "fulfillmentState": "Fulfilled",
                "message": {"contentType": "PlainText", "content": "Your balance is $1,000."}
            }
        }
```

### Step 4: Assign Lambda to Lex
- Return to **Lex Console**.
- Select the **Banker Chatbot** and choose the **Fulfillment** section.
- Select **AWS Lambda Function** and link the created Lambda function.

## 🎯 Best Practices
- **IAM Role:** Ensure the Lambda function has the correct IAM role for Lex integration.
- **Error Handling:** Implement proper error messages in the Lambda code.
- **Testing:** Test your Lambda function independently before linking it to Lex.

---

This completes the Lambda integration for the AWS Banker Chatbot. ✅

# AWS Banker Chatbot Technical Documentation (Part 4: Saving User Info)

This document outlines how to save and manage user information in the AWS Banker Chatbot using Amazon Lex and DynamoDB.

## 📦 Overview
Saving user information allows the chatbot to personalize responses and keep track of user interactions for improved service.

## 🛠️ Creating the DynamoDB Table
### Step 1: Access DynamoDB
- Log in to the **AWS Console**.
- Navigate to **DynamoDB**.
- Click **Create Table**.
- Set **Table Name:** `BankerUserInfo`.
- Set **Primary Key:** `userId` (String).

### Step 2: Configure Table Settings
- Enable **On-Demand Capacity**.
- Create the table and wait for it to be available.

## 🧩 Integrating DynamoDB with Lambda
### Step 1: Update Lambda Function Permissions
- Go to the **Lambda Console**.
- Select the Lambda function connected to Lex.
- Attach the **AmazonDynamoDBFullAccess** policy.

### Step 2: Update Lambda Code
```python
import boto3
import json

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('BankerUserInfo')

def lambda_handler(event, context):
    user_id = event['userId']
    user_name = event['currentIntent']['slots']['UserName']
    
    table.put_item(
        Item={
            'userId': user_id,
            'userName': user_name
        }
    )
    return {
        'dialogAction': {
            'type': 'Close',
            'fulfillmentState': 'Fulfilled',
            'message': {'contentType': 'PlainText', 'content': f'Thanks {user_name}, your data has been saved!'}
        }
    }
```

### Step 3: Link Lambda to Lex
- Go to **Lex Console**.
- Choose your **Banker Chatbot**.
- Assign the updated Lambda function under **Fulfillment**.

## 🎯 Best Practices
- **Security:** Limit DynamoDB access with least privilege IAM roles.
- **Data Management:** Implement data retention policies for compliance.
- **Testing:** Validate the data persistence using the DynamoDB console.

---

This completes the user information saving setup for the AWS Banker Chatbot. ✅

# AWS Banker Chatbot Technical Documentation (Part 5: Creating a Chatbot with Multiple Slots)

This document covers how to create an AWS Banker Chatbot using **Amazon Lex** with multiple slots for collecting user data efficiently.

## 📦 Overview
Slots are used to gather specific information from the user during a conversation. A chatbot with multiple slots enables more dynamic and comprehensive interactions by collecting multiple data points within a single intent.

## 🛠️ Defining Multiple Slots in Lex
### Step 1: Access Lex Console
- Log in to the **AWS Console**.
- Navigate to **Amazon Lex** and select your **Banker Chatbot**.

### Step 2: Add Slots to an Intent
- Choose an existing intent or create a new one.
- Add multiple slots under the **Slots** section.
- Provide slot names and types (e.g., `AccountType`, `Amount`, `Date`).

### Step 3: Define Prompts for Each Slot
- For each slot, specify a **Prompt Message** to request user input.
- Example prompts:
  - `AccountType`: "What type of account would you like to check?"
  - `Amount`: "How much would you like to transfer?"
  - `Date`: "What date should the transfer be scheduled for?"

### Step 4: Set Slot Validation
- Use **slot types** to restrict valid inputs (e.g., `AMAZON.NUMBER` for numeric amounts).

### Step 5: Assign Fulfillment with Lambda (Optional)
- Link the slots to a Lambda function to process the collected data.

## 📖 Example Lambda Code for Multi-Slot Handling
```python
import json

def lambda_handler(event, context):
    slots = event['currentIntent']['slots']
    account_type = slots['AccountType']
    amount = slots['Amount']
    date = slots['Date']

    message = f"You have requested to transfer {amount} from your {account_type} account on {date}."
    
    return {
        'dialogAction': {
            'type': 'Close',
            'fulfillmentState': 'Fulfilled',
            'message': {'contentType': 'PlainText', 'content': message}
        }
    }
```

## 🎯 Best Practices
- **Use Clear Prompts:** Ensure prompts are user-friendly and clear.
- **Slot Dependencies:** Use slot elicitation for logical ordering of questions.
- **Testing:** Thoroughly test each slot for proper value collection and validation.

---

This completes the setup for creating a chatbot with multiple slots in the AWS Banker Chatbot project. ✅



