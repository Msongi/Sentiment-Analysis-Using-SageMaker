# Deploying on the web app

### Endpoint steps
You can start an endpoint by calling .deploy() on an estimator and passing in some information about the instance.
    
    xgb_predictor = xgb.deploy(initial_instance_count = 1, instance_type = 'ml.m4.xlarge'
Then, you need to tell your endpoint, what type of data it expects to see as input (like .csv).

      from sagemaker.predictor import csv_serializer
      xgb_predictor.content_type = 'text/csv'
      xgb_predictor.serializer = csv_serializer
Then, perform inference; you can pass some data as the "Body" of a message, to an endpoint and get a response back!
      
      response = runtime.invoke_endpoint(EndpointName = xgb_predictor.endpoint,   # The name of the endpoint we created
                                       ContentType = 'text/csv',                     # The data format that is expected
                                       Body = ','.join([str(val) for val in test_bow]).encode('utf-8'))
The inference data is stored in the "Body" of the response, and can be retrieved:
      
      response = response['Body'].read().decode('utf-8')
      print(response)

### Create a Lambda Function

The steps to create a lambda function are outlined below:

This Lambda function will be executed whenever our public API has data sent to it. When it is executed it will receive the data, perform any sort of processing that is required, send the data (the review) to the SageMaker endpoint we've created and then return the result.

Part A: Create an IAM Role for the Lambda function

Since we want the Lambda function to call a SageMaker endpoint, we need to make sure that it has permission to do so. To do this, we will construct a role that we can later give the Lambda function.

### Builld an API Gateway
At this point we've created and deployed a model, and we've constructed a Lambda function that can take care of processing user data, sending it off to our deployed model and returning the result. What we need to do now is set up some way to send our user data to the Lambda function.

The way that we will do this is using a service called API Gateway. Essentially, API Gateway allows us to create an HTTP endpoint (a web address). In addition, we can set up what we want to happen when someone tries to send data to our constructed endpoint.

### Deploy the Web app
Once the backend is done, i.e the scripts that take the training data and clean and build a model on it, the endpoint, lambda function and API gateway have been deployed. You can deploy the model which will run on AWS and you can open a new web browser, go to index.html and interact with your deployed model. 

### Deleting the End point
Always make sure to shut down your endpoint when you are done using it
