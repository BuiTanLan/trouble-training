<h3 align="center">Full Stack Workshop (Frontend + Backend + Monitoring) </h3>

<p align="center">
  <img  width="900" src="Doc/Assets/stack_logos.png" alt="Workshop stack tools logos" >
</p>
<p align="center" > The application contains a full frontend and backend implementation with infrastructure and app monitoring. There are several patterns used such as. <b>Mediator, BFF, Domain</b> etc.. and everything is secured with <b>Identity server</b> fully supporting <b>OpenId Connect</b> and <b>OAuth2.0</b></p>
<br />



<p align="center" >Demo Application contains small UI, where you can manage your WebHooks</p>


<p align="center">
  <img width="600" src="Doc/Assets/WorkshopUI.gif" alt="Workshop UI example" >
</p>

### Stack
 - **Frontend**  - [React](https://reactjs.org/), [Relay](https://relay.dev/), [Typecsript](https://www.typescriptlang.org/), [ReactRouter](https://reactrouter.com/), [TailwindCSS](https://tailwindcss.com/)
 - **Backend** - [Netcore](https://dotnet.microsoft.com/), [Hotchocolate](https://chillicream.com), [IdentityServer](https://duendesoftware.com/products/identityserver), [ElasticSearch](https://www.elastic.co/), [Opentelemerty](https://opentelemetry.io/), [Serilog](https://serilog.net/), [MediatR](https://github.com/jbogard/MediatR), [Hangfire](https://www.hangfire.io/), [Automapper](https://automapper.org/), [Fluentvalidation](https://fluentvalidation.net/), [Docker](https://www.docker.com/), [Entity Framework](https://docs.microsoft.com/cs-cz/ef/)

### Integration steps:
Please follow these steps if you want to understand it because of the connection between the parts:

1) [Configure logging](Doc/Logging.md) - I`ll show you how to set up a proper distributed logging solution for .Net
2) [Configure telemetry](Doc/OpenTelemetry.md) - I`ll show you how to properly set up Opentelemetry and Elastic APM.
3) [Configure idenity](Doc/Identity.md) - You`ll learn how to use the BFF pattern to secure your application.
4) [Configure monitoring](Doc/ElasticSearch.md) - You will learn how to monitor the entire app stack (this depends on parts 1 and 2).


<br />
<p align="center">

<img src="Doc/Assets/workshop_architecture.png" alt="Workshop architecture" >

<br />

</p>


**Monitoring example**

<p align="center">

<img src="Doc/Assets/elastic_apm_example.png" alt="Apm distributed tracing" >

</p>

<br />

### Quick setup

- [Quick setup](#quick-setup)
  - [Instal Net SDK](#instal-net-sdk)
  - [Download docker](#download-docker)
  - [Docker images](#docker-images)
  - [Setup Database](#setup-database)
  - [Migrations](#migrations)
  - [Run Frontned and Backend](#run-frontned-and-backend)
  - [Run Elastic and Beats](#run-elastic-and-beats)


To run this stack locally, you need to ensure the following:
- Make sure you have NET SDK installed. 
- You need to install Docker. Most of the stack runs in a Docker container.
- You need to run all prepared `docker-compose` files to populate all images for the monitoring platform.
- You need to set up PostgreSQL databases (Create any DB server). 
- You need to migrate all DBs (each project has its own migration folder).
- Run the NetCore project from the terminal or back it up to Docker.

</br>

##### Instal Net SDK

Make sure you [have installed](https://dotnet.microsoft.com/download) Net 5.0 SDK. You can verify installed SDK version by running: `dotnet --list-sdks` in your terminal.

```sh
#Example output on Windows
PS C:\Users\dakupc> dotnet --list-sdks
5.0.100 [C:\Program Files\dotnet\sdk]
5.0.201 [C:\Program Files\dotnet\sdk]
```
</br>

##### Download docker

Based on your system download and [install docker](https://docs.docker.com/engine/install/)

</br>

##### Docker images

In folder `/Docker` you can find prepared images for:
- `Elasticsearch` - Elastic, Kibana, OtelCollector, APMserver, Logstash
- `Beats` - FileBeat, HeartBeat, MetricBeat, PacketBeat
- `PostgresSQL` - Database


In each folder you will find `docker-compose.yml` and you need to run `docker-compose up` command from your terminal. The setup was tested on WSL Docer Desktop.

</br>
##### Setup Database

Make sure PostgreSQL is running. The following main and related databases need to be configured:

*APIServer Database*
```sh
Host: localhost
DatabaseName: ApiDB
Port: 6543
Username: postgres
PasswordL postgres
```

*IdentityServer Database*
```sh
Host: localhost
DatabaseName: IdentityDB
Port: 6543
Username: postgres
PasswordL postgres
```

*Scheduler Database*
```sh
Host: localhost
DatabaseName: Scheduler
Port: 6543
Username: postgres
PasswordL postgres
```

Preconfigured connection strings:

```json
"ConnectionStrings": {
  "HangfireConnection": "Host=localhost;Port=6543;Database=Scheduler;Username=postgres;Password=postgres",
  "ApiDbContext": "Host=localhost;Port=6543;Database=ApiDB;Username=postgres;Password=postgres",
  "AppIdnetityDbContext": "Host=localhost;Port=6543;Database=IdentityDB;Username=postgres;Password=postgres",
  "AppIdnetityDbContext": "Host=localhost;Port=6543;Database=IdentityDB;Username=postgres;Password=postgres",
  "Elasticsearch": "http://admin:admin@localhost:9200",
  "Opentelemetry": "http://localhost:55680"
},
```

Run the database its required by next step!
</br>

##### Migrations

Make sure you have installed [Entity Framework Core tools](https://docs.microsoft.com/en-us/ef/core/cli/dotnet) to perform any necessary migrations.

```sh
dotnet tool install --global dotnet-ef
```

If you run  `dotnet ef` in terminal yoy shoud get:

```
                     _/\__       
               ---==/    \\      
         ___  ___   |.    \|\    
        | __|| __|  |  )   \\\   
        | _| | _|   \_/ |  //|\\ 
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 5.0.9
```


Go to dirrectory: `APIServer/Persistence` and run following commands:

```
 dotnet ef database update
```

Go to dirrectory: `IdentityServer/Persistence` and run following commands:
```
 dotnet ef database update --context AppConfigurationDbContext
 dotnet ef database update --context AppPersistedGrantDbContext
 dotnet ef database update --context AppIdnetityDbContext
```
</br>

##### Run Frontned and Backend

Since all migrations, databases and infrastructures are prepared, you can start each  project from the respective directory. You can also put each application in a docker container and launch it from there. This is entirely up to you.

> &#10240;
> **NOTE:** Make sure you trust developer certificates: `dotnet dev-certs https --trust`. This is only for development needs make sure you properly setup certificate in production!
> &#10240;

Go to dirrectory: `APIServer/API` and run:
1) `dotnet restore`
2) `dotnet watch run`

Go to dirrectory: `IdentityServer/API` and run:
1) `dotnet restore`
2) `dotnet watch run`

Go to dirrectory: `BFF/API` and run:
1) `dotnet restore`
2) `dotnet watch run`

> &#10240;
>**NOTE:** Restoring and running will take some time, especially for BFF, since the frontend is fully recompiled and served by the BFF backend using static files.
> &#10240;

</br>


**APIServer** runs on: `https://localhost:5022`

**IdentityServer** runs on: `https://localhost:5001`

**BFF** runs on: `https://localhost:5015`

</br>

##### Run Elastic and Beats

Start the elasticsearch container group and then beats. Please stick to this order to avoid exception logging due to a missing connection. You can also put everything into a Docker image and wait until the previous section is complete.



To run all containers, please make sure you have enough RAM and a good computer - this is not for kids :P Have fun :)