#Compute Dashboard

##System Requirements

To build this application please ensure you have the following installed on your system:

* Java SE Development Kit 8 or higher
* Maven version 3.3.9 or higher
* Docker version 19.03.8 or higher

To be able to successfully make a request via the locally running application you must also have:

* An accessKeyId and secretAccessKey for an AWS account


##Running the Compute Dashboard Application Locally

###How to build

There are two options for building and running the application locally. In both cases the application
will use the aws.accessKeyId and aws.secretAccessKey system properties. These are required by the AWS
Java SDK as it needs to authenticate to an AWS account to be able to call the endpoints that return
the EC2 instance data.

####Docker
First build the compute-dashboard-api:
```
mvn clean install
```

Then build theDocker image:
```
docker build -f Docker/Dockerfile -t compute-dashboard-api:latest .
```

Next, edit the docker-compose.yml file and replace the placeholder text ACCESS_KEY_ID SECRET_ACCESS_KEY with your own credentials

Finally you can run the compute-dashboard-api Docker image using docker-compose from within the Docker directory:

```
cd Docker
docker-compose up
```

####Spring Boot Application
Alternatively you can build the application via maven and run as a Spring Boot app without the need for Docker

First build the compute-dashboard-api:
```
mvn clean install
```

Then run the application (replace the placeholder text ACCESS_KEY_ID SECRET_ACCESS_KEY with your own credentials):
```
java -Daws.accessKeyId=ACCESS_KEY_ID -Daws.secretAccessKey=SECRET_ACCESS_KEY -jar target/compute-dashboard-api-0.0.1-SNAPSHOT.jar

```

###Code Coverage via Mutation Testing
As it is actually able to detect whether each statement is meaningfully tested, mutation testing is the 
gold standard against which all other types of coverage are measured.

https://pitest.org/

Mutation testing can be run via pitest by running the following:

```mvn org.pitest:pitest-maven:mutationCoverage```

A test report can be found under: target/pit-reports

Currently a line coverage of 98% is achieved.


###Manually Test the Local Compute Dashboard API Application
Once you have the application running successfully on your machine using one of the two approaches described above
you can manually interact with the API via a REST client of your choice.

####Swagger UI
When the application is running locally the springdoc-openapi-ui maven plugin provides a Swagger UI which allows 
visual exploration of the API and invokation of endpoints manually with user defined payloads:
 
http://localhost:8080/swagger-ui.html

####cURL
Assuming you have curl on your machine you can try the following from the command prompt.

```
curl http://localhost:8080/api/ec2metadata/eu-west-1?sortBy=state
```


##How to access the pre-deployed Compute Dashboard API
The Compute Dashboard API has also been deployed onto my AWS account. The documentation specified that the endpoint
is secured "using AWS keys for authentication". To achieve this I deployed the application as a Docker container
running on an EC2 instance behind AWS API Gateway. An Api key was generated and AWS API Gateway is configured to 
require this API token in order to allow traffic through to the REST API endpoints. The API token must be supplied
in the x-api-key header as illustrated in the curl command below:

```
curl https://t1zyum7gtc.execute-api.eu-west-1.amazonaws.com/v1/api/ec2metadata/eu-west-1 -H 'x-api-key: ajtZrHeCsyaWGmo9vGPcb6wK3ClGAbJp2Im2nc0m'
```

##Paging and Sorting
The specification stated that results should be pagable and sortable. This is achieved via the page, pageSize and 
sortBy parameters as detailed in the Open API 3.0 schema that can be found in OpenAPISchema/api-docs.yaml

##Security
The specification stated that we should "consider security of requests and responses". You will note that the endpoint
exposed by the application deployed to AWS can only be accessed via HTTPS and as a result requests and responses are 
secure while in transit. Security is discussed further in the system architecture PDF.

##Technology Stack and System Architecture
Please refer to the PDF document for a full description of the technologies used and the overall system architecture
including the AWS infrastructure that was provisioned in order to deploy the application on AWS.


