# C# Sample Accelerator

A sample accelerator for C#.

This sample is the Weather Forecast RESTful API application made available from Microsoft.

The starting source for this sample was created using:
```
$ dotnet new webapi --framework net6.0 --language C#
```

## Running the app locally

To run the sample application:

```
$ dotnet run
```

## Deploying to Kubernetes as a TAP workload with Tanzu CLI

> NOTE: The provided `config/workload.yaml` file uses the Git URL for this sample. When you want to modify the source, you must push the code to your own Git repository and then update the `spec.source.git` information in the `config/workload.yaml` file.

If you make modifications to the source, push these changes to your own Git repository.

When you are done developing your app, you can simply deploy it using:

```
tanzu apps workload apply -f config/workload.yaml
```

If you would like deploy the code from your local working directory you can use the following command:

```
tanzu apps workload create sample-app -f config/workload.yaml \
  --local-path . \
  --source-image <REPOSITORY-PREFIX>/sample-app-source \
  --type web
```

## Accessing the app deployed to your cluster

Determine the URL to use for the accessing the app by running:

```
tanzu apps workload get sample-app
```

To access the deployed app use the URL shown under "Workload Knative Services" and append the endpoint `/weatherforecast` to that URL.

This depends on the TAP installation having DNS configured for the Knative ingress.

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
  --memory 1Gi
```
> Note: The app will take around 2-3 minutes to create.

### Build and Deploy the Application

Deploy and build the application, specifying its required parameters

```shell
az spring app deploy --name ${SERVICE_APP} \
    --source-path weatherforecast-csharp 
```
> Note: Deploying the application will take 5-10 minutes

### Test the Application

Run the following commands

```shell
export APP_URL=$(az spring app show --name ${SERVICE_APP} --query properties.url | tr -d '"')

curl "${APP_URL}/WeatherForecast"
```
