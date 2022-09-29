# spring-cloud-serverless repo

This repo provides a simple serverless Hello web app based on Spring Boot and Spring Cloud Function.

It can be deployed as a standalone web app, as a Tanzu Application Platform workload resource or, as a Kubernetes Deployment and Service.

## The code

> **NOTE**: The project is configured for Java 11, if you prefer a different version, then modify the `java.version` property in `pom.xml`.

The project contains the following Function bean definition:

```text
	@Bean
	public Function<String, String> hello() {
		return (in) -> {
			return "Hello " + in;
		};
	}
```

This simple serverless app returns the input value, prefixed with "Hello ". This is just a simple example what a Spring Cloud Function app can do. 
It is defined in `src/main/java/com/example/helloapp/HelloAppApplication.java`

## Deployment

This app can be deployed as a stand-alone web app, as a Tanzu Application Platform (TAP) workload resource or, as a Kubernetes Deployment and Service.

### Standalone app with embedded Tomcat server

You can build the project using Maven:

```bash
mvn clean package
```

To run the app using the embedded Tomcat server you can run this command:

```bash
mvn spring-boot:run
```

You can access the app using `curl`:

```bash
curl -w'\n' -H 'Content-Type: text/plain' localhost:8080 -d "Fun"
```

## Deploying to Azure Spring Apps with Azure CLI

### Login to Azure

Login to the Azure CLI and choose your active subscription. 

```shell
az login
az account list -o table
az account set --subscription ${SUBSCRIPTION}
```

### Create Azure Spring Apps service instance

Prepare a name for your Azure Spring Apps service.  The name must be between 4 and 32 characters long and can contain only lowercase letters, numbers, and hyphens.  The first character of the service name must be a letter and the last character must be either a letter or a number.

Create a resource group to contain your Azure Spring Apps service.

> Note: This step can be skipped if using an existing resource group

```shell
az group create --name ${RESOURCE_GROUP} \
    --location ${REGION}
```

Accept the legal terms and privacy statements for the Enterprise tier.

> Note: This step is necessary only if your subscription has never been used to create an Enterprise tier instance of Azure Spring Apps.

```shell
az provider register --namespace Microsoft.SaaS
az term accept --publisher vmware-inc --product azure-spring-cloud-vmware-tanzu-2 --plan asa-ent-hr-mtr
```

Create an instance of Azure Spring Apps Enterprise.

```shell
az spring create --name ${SPRING_APPS_SERVICE} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${REGION} \
    --sku Enterprise
```

> Note: The service instance will take around 10-15 minutes to deploy.

Set your default resource group name and cluster name using the following commands:

```shell
az configure --defaults \
    group=${RESOURCE_GROUP} \
    location=${REGION} \
    spring=${SPRING_APPS_SERVICE}
```

### Create the application in Azure Spring Apps

Create an application:

```shell
az spring app create --name ${SERVICE_APP} \
  --assign-endpoint true \
  --instance-count 1 \
  --memory 1Gi
```
> Note: The app will take around 2-3 minutes to create.

### Build and Deploy the Application

Deploy and build the application, specifying its required parameters

```shell
az spring app deploy --name ${SERVICE_APP} \
    --source-path spring-cloud-serverless
```
> Note: Deploying the application will take 5-10 minutes

### Test the Application

Run the following commands

```shell
export APP_URL=$(az spring app show --name ${SERVICE_APP} --query properties.url | tr -d '"')

curl "${APP_URL}"
```
