Red Hat Microsweeper demo
=========================

This demo uses a number of cloud technologies to implement a simple game from the earlier days of computing: Minesweeper!

Technologies include:

* JQuery-based Minesweeper written by [Nick Arocho](http://www.nickarocho.com/) and [available on GitHub](https://github.com/nickarocho/minesweeper).
* Backend baed on [MicroProfile](https://microprofile.io) and [Thorntail](https://thorntail.io) (part of [Red Hat OpenShift Application Runtimes](https://developers.redhat.com/products/rhoar/overview/) to persist scoreboard
* Deployed using [Red Hat OpenShift](https://openshift.com) and the [Open Service Broker](https://www.openservicebrokerapi.org/) to deploy [Cosmos DB](https://azure.microsoft.com/en-us/services/cosmos-db/) on [Microsoft Azure](https://azure.microsoft.com/).


To run demo
-----------
These steps are necessarily high level and encompass many smaller steps that will be added later.

* Get an [Azure Account](https://azure.microsoft.com/en-us/free/) (e.g. using an Azure Pass)
* Use an OpenShift OKD [all-in-one installer](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fcealsair%2FMicroProfileOnAzure%2Fmaster%2Fallinone.json) to deploy to Azure. Takes about 20 minutes.
* Install the [Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure) version 0.11.0 (use the helm charts `--version 0.11.0` option) to your OpenShift instance
 * Note the latest Open Service Broker has temporarily removed Cosmos DB as it's considered experimental!
* Provision an instance of [CosmosDB](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction) to a new OpenShift project using the Service Broker, and elect to create a _binding_ to a new project
* Deploy this sample app to that project via:
 * `mvn clean package fabric8:deploy`
* Edit the `microsweeper` Deployment Config and add new environment variables based on the newly-created secret from the service instance.
 * Be sure to use a `SCORESDB_` prefix when adding the env vars


Then run `mvn clean package fabric8:deploy` to deploy.

Once deployed access the UI using the route exposed via OpenShift. _And re-live your youth like a boss._

Eye Candy
=========

_Service Catalog in OpenShift_

![Service Catalog](docs/osb.png)

-----

_Deploying Azure service through OpenShift Service Catalog_

![Service Catalog](docs/wizard.png)

-----

_OpenShift Project with deployed service_

![Service Catalog](docs/project.png)

-----

_Money Shot_

![Service Catalog](docs/minesweeper.png)

-----


_Browsing Minesweeper scoreboard from Azure Portal_

![Service Catalog](docs/portal.png)

Sample Dependencies
```xml
		<!-- microprofile -->
        <dependency>
            <groupId>io.thorntail</groupId>
            <artifactId>microprofile-health</artifactId>
        </dependency>
        <dependency>
            <groupId>io.thorntail</groupId>
            <artifactId>microprofile-config</artifactId>
        </dependency>
```

```sh
oc set probe dc/microsweeper --liveness --readiness --get-url=http://:8080/health --initial-delay-seconds=30 --period-seconds=15 --timeout-seconds=5 --failure-threshold=5
```
