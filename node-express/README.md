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

# node-express

This is a starter ExpressJs project, you can run it as a standalone
app using `npm run server` in the root of the project.
The server will be listening to request on port `3000`,
so you can test the server in a browser accessing `http://localhost:3000` or via `cURL`.

Before running the command `npm run server` you need to run `npm install` to
install the dependencies.

## Deploying to Azure Spring Apps with Azure CLI

Here, we will deploy the application on Azure Spring Apps, ensure that all prerequisites are met

Prerequisites:

* Completion of [Create Azure Spring Apps service instance](../SPRING_APPS.md)

### Create the application in Azure Spring Apps

Create an application:

```shell
az spring app create --name ${SERVICE_APP} \
  --assign-endpoint true \
  --instance-count 1 \
  --memory 1Gi \
  --env "PORT=8080"
```
> Note: The app will take around 2-3 minutes to create.

### Build and Deploy the Application

Deploy and build the application, specifying its required parameters

```shell
az spring app deploy --name ${SERVICE_APP} \
    --source-path node-express
```
> Note: Deploying the application will take 5-10 minutes

### Test the Application

Run the following commands

```shell
export APP_URL=$(az spring app show --name ${SERVICE_APP} --query properties.url | tr -d '"')

curl "${APP_URL}"
```
