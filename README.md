# Deploying AWS Lambda RESTful APIs

ref: https://ig.nore.me/2016/03/setting-up-lambda-and-a-gateway-through-the-cli/

## Deploying AWS Lambda Functions

Steps needed to deploy AWS Lambda functions as RESTful APIs:

First, you will need to create the script that will act as the Lambda function. There is one script available in this GitHub repository. It's called [`index.js`](./index.js).

After creating your script, you will need to zip it so you can upload to AWS from your local machine:

```bash
bashzip index.zip index.js
```

## Configuring the AWS API Gateway

Then, you can use the `aws` CLI tool to create a new RESTful API:

```bash
aws apigateway create-rest-api \
    --name hey \
    --profile brunokrebs
```

> **Note:** All commands in this file are for reference only. You will need to replace the values shown here (e.g. `--profile`, `--parent-id`, etc) with your own values.

After defining the the RESTful API, you will need to define a resource:

```bash
aws apigateway create-resource \
    --rest-api-id jahei62xk4 \
    --parent-id ik8u3icsnf \
    --path-part there \
    --profile brunokrebs
```

As you can see, to create a resource, you will need a `--parent-id`. To get this id, issue the following command:

```bash
aws apigateway get-resources --rest-api-id jahei62xk4 --profile brunokrebs
```

Then, with both the RESTful API and the resource in place, you will need to create a new request method to indicate what the API can do:

```bash
aws apigateway put-method \
    --rest-api-id jahei62xk4 \
    --resource-id pfvpzo \
    --http-method GET \
    --authorization-type "NONE" \
    --profile brunokrebs
```

Then, after defining what request methods a resource can handle, you will have to define how it responds to requests:

```bash
aws apigateway put-method-response \
    --rest-api-id jahei62xk4 \
    --resource-id pfvpzo \
    --http-method GET \
    --status-code 200 \
    --profile brunokrebs
```

Finally, you will define the integration of this resource with your AWS Lambda function:

```bash
aws apigateway put-integration \
    --rest-api-id jahei62xk4 \
    --resource-id pfvpzo \
    --http-method GET \
    --type AWS \
    --integration-http-method POST \
    --uri arn:aws:apigateway:sa-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:sa-east-1:128090157610:function:hello-world/invocations \
    --profile brunokrebs
```

```bash
aws apigateway put-integration-response \
    --rest-api-id jahei62xk4 \
    --resource-id pfvpzo \
    --http-method GET \
    --status-code 200 \
    --selection-pattern ".*"  \
    --profile brunokrebs
```

Deploy:

```bash
aws apigateway create-deployment \
    --rest-api-id jahei62xk4 \
    --stage-name prod \
    --stage-description 'Production' \
    --description 'First deployment' \
    --profile brunokrebs
```

```bash
aws lambda add-permission \
    --function-name hello-world \
    --statement-id "apigateway-igor-test-3" \
    --action "lambda:InvokeFunction" \
    --principal "apigateway.amazonaws.com" \
    --source-arn "arn:aws:execute-api:sa-east-1:128090157610:jahei62xk4/*/GET/there" \
    --profile brunokrebs
```