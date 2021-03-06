﻿# SQL Server Connector Sample App - EntityFramework Core

ASP.NET Core sample app illustrating how to use the EntityFramework Core together with [Steeltoe SQL Server Connector](https://github.com/SteeltoeOSS/Connectors) for connecting to a SqlServer service on CloudFoundry.

## Pre-requisites - CloudFoundry

1. Installed Pivotal CloudFoundry
1. Optionally, installed Windows support
1. A SQL Server Instance (and optionally the SQL Server Tile installed in CloudFoundry **- Coming Soon**)
1. Install .NET Core SDK

## Create Sql Service Instance on CloudFoundry

You must first create an instance of the SQL Server service in a org/space using either a user provided service ('cf cups...' replacing values between pipes as appropriate) OR bind a service using the SQL Server Connector

```bash
> cf target -o myorg -s development
> cf cups mySqlServerService -p '{\"pw\": \"|password|\",\"uid\": \"|user id|\",\"uri\": \"jdbc:sqlserver://|host|:|port|;databaseName=|database name|\"}'
# or
> cf create-service SqlServer sharedVM mySqlServerService
```

## Publish App & Push to CloudFoundry

```bash
> cf target -o myorg -s development
> cd samples/Connectors/src/AspDotNetCore/SqlServerEFCore
> dotnet restore --configfile nuget.config
# Publish app to a directory selecting the framework and runtime you want to run on
> dotnet publish -f netcoreapp2.0 -r ubuntu.14.04-x64
# Push the app using the appropriate manifest.
> cf push -f manifest.yml -p bin/Debug/netcoreapp2.0/ubuntu.14.04-x64/publish
# or
> cf push -f manifest-windows.yml -p bin/Debug/netcoreapp2.0/win10-x64/publish
```

> Note: The provided manifest will create an app named `sqlserverefcore-connector` and attempt to bind to the the app to SQL Server service `mySqlServerService`.

## What to expect - CloudFoundry

After building and running the app, you should see something like the following in the logs.

To see the logs as you startup and use the app: `cf logs sqlserverefcore-connector`

On a Windows cell, you should see something like this during startup:

```text
2016-07-01T07:27:49.73-0600 [CELL/0]     OUT Creating container
2016-07-01T07:27:51.11-0600 [CELL/0]     OUT Successfully created container
2016-07-01T07:27:57.73-0600 [APP/0]      OUT Hosting environment: development
2016-07-01T07:27:57.73-0600 [APP/0]      OUT Content root path:  /home/vcap/app
2016-07-01T07:27:57.73-0600 [APP/0]      OUT Now listening on: http://*:8080
2016-07-01T07:27:57.73-0600 [APP/0]      OUT Application started. Press Ctrl+C to shut down.
```

At this point the app is up and running.  Upon startup the app inserts a couple rows into the bound SQL Server database. Those rows are displayed on the home page of the application.

---

### See the Official [Steeltoe Service Connectors Documentation](https://steeltoe.io/docs/steeltoe-service-connectors) for a more in-depth walkthrough of the samples and more detailed information