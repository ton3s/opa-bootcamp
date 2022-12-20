FROM amazon/aws-lambda-provided:al2

# Update image and install JQ to parse json
RUN yum -y update
RUN yum -y install jq

# Create OPA directory, download OPA, and copy bundle
RUN mkdir /opa
WORKDIR /opa
RUN curl -L -o opa https://openpolicyagent.org/downloads/v0.37.2/opa_linux_amd64_static
RUN chmod +x opa
COPY . .


# Create runtime directory and copy shell scripts
WORKDIR /var/runtime
COPY start.sh .
COPY bootstrap.sh .
RUN chmod +x bootstrap.sh
RUN chmod +x start.sh

# Start Handler
ENTRYPOINT ["/var/runtime/start.sh"]