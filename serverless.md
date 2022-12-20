# OPA in AWS Lambda

- OPA with AWS API gateway and AWS Lambda (https://aws.amazon.com/blogs/opensource/easily-running-open-policy-agent-serverless-with-aws-lambda-and-amazon-api-gateway/)

- Follow the steps in the link above to create the files required

### Build docker image

```
## Authenticate docker client to AWS ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 969983227143.dkr.ecr.us-east-1.amazonaws.com

## If having issues with storing the credentials
rm ~/.docker/config.json

## Edit line 13 in docker file to the following
COPY . .

## Build the docker image
docker build -t opa-docker-serverless .

## Tag docker image
docker tag opa-docker-serverless:latest 969983227143.dkr.ecr.us-east-1.amazonaws.com/opa-docker-serverless:latest

## Push docker image
docker push 969983227143.dkr.ecr.us-east-1.amazonaws.com/opa-docker-serverless:latest
```

## Configure API Gateway

```
## Configure mapping template for integration request
{
"x_opa_path" : "$input.params("proxy")",
"x_opa_method": "$context.httpMethod",
"x_opa_payload": $input.body
}

## Configure mappings method response
200
```

## Test API Gateway

```
 curl -XPOST "https://lc3jbxd966.execute-api.us-east-1.amazonaws.com/opa/v0/data/hello" \
 -H 'Content-Type: application/json' \
 -d '{"lang": "en"}'

{"hello":"hola mundo"}%

curl -XPOST "https://opa.antoniocunanan.com/v0/data/hello" \
 -H 'Content-Type: application/json' \
 -d '{"lang": "en"}'

```

## Test Lambda locally (WIP)

```
docker run -v ~/.aws-lambda-rie:/aws-lambda -p 9000:8080 \
  --entrypoint /aws-lambda/aws-lambda-rie 969983227143.dkr.ecr.us-east-1.amazonaws.com/opa-docker-serverless "/var/runtime/start.sh"

 curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" -d '{"x_opa_path": "/v0/data/hello","x_opa_method": "POST","x_opa_input": {"lang": "en"}}'
```
