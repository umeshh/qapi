# qAPI - Translating database queries into API calls

qAPI is a JHipster project created by T-Mobile. qAPI allows users to transfer database connections and queries from their tests to an API service, which testers can integrate to retrieve and validate data from a database through API calls.

## Getting Started

Below are instructions that will help you get started in getting the qAPI up and running.

### Prerequisites

| Prerequisite | Command to verify | Description |
| ------------ | -------------- | -----------  |
| Java Runtime Environment (JRE) 1.8.x | java -version | As it is a Java project, Java needs to be installed locally.
| Maven | mvn --version | Maven is needed to download project dependencies.
| T-Vault (Optional) | N/A | The project uses T-mobile's open sourced secret management tool T-Vault, for maintaining all database credentials. Please refer to https://github.com/tmobile/t-vault for more information on T-Vault.
| Oracle JDBC driver | N/A | The oracle JDBC driver needs to be retrieved from the Oracle website, and once accepting the license terms, the jar can be saved on your local machine/artifactory. Oracle website: https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html Instructions to have pom dependency working: https://www.mkyong.com/maven/how-to-add-oracle-jdbc-driver-in-your-maven-local-repository/
| Git (Optional) | N/A | You can use git to clone the project locally, or choose to simply download the project as a zip file. To clone the project, using your favorite git GUI tool or from your command line, type: `git clone https://github.com/tmobile/qapi.git` 

### Configuration

Below are configurations that need to be set before starting up qAPI.

#### File: resources\config\application.yml

At the bottom of the file, you will see a section for application -> configuration
 
Here, you will configure the following values:
- secretManagement (Configuration for secret management)
    - encryptionPassword: Used to decrypt zip file containing secret files if t-vault is not enabled. Can be blank if t-vault is enabled.
    - tvaultEnabled: Defaulted to false. If set to true, t-vault will be used to manage secrets.
- tvault: (Config for T-Vault. Values can be left blank if t-vault is not enabled)
    - roleId: T-Vault parameter to authenticate via approle
    - secretId: T-Vault parameter to authenticate via approle
    - tvaultHost: Host url to your t-vault instance. Example: ` https://myt-vault.myorg.com `
- repoconfiguration: (Configuration for user specified Repository for query config files)
    - location: local # Set to 'local' or 'repository', based on where the query config files are maintained
    - repoLocation: Repository where the queries are configured (Git link)
    - repoRoot: Root folder/folders to where the config files live in the repository
    - branch: The branch of the config repository to be used

Note, that using T-vault is not mandatory, but is the default way of managing secrets in qAPI. If you choose to implement another secret management tool, changes will have to be made in the RoutingDataSourceConfiguration.java class.

Additionally, in the repository configured under 'repoLocation', you must have at least one config file.
For more instructions on how to write a config file, follow this wiki step: [Repository for query config files](https://github.com/tmobile/qapi/wiki/Repository-for-query-config-files)

#### Files: resources\config\application.dev.yml, resources\config\application-prod.yml

Here, you will configure your eureka service url under eureka -> client -> defaultzone 

You can also configure your secret token that will be registered on your jhipster registry under jhipster -> security -> base64-secret

#### Files: resources\config\bootstrap.yml, resources\config\bootstrap-prod.yml

Here, you will configure the jhipster registry password, as well as the cloud config uri.

#### Files: resources\secrets.zip

If you are using the default method of managing secrets with a zip in the local root, place a zip file called "secrets.zip", that is encryped with AES and password protected. This can be done with common programs such as 7-Zip. For more information on the structure of these files, please visit the wiki Sub-Page: [Locally Encrypted Secret Files](https://github.com/tmobile/qapi/wiki/Locally-encrypted-secret-files)

 #### (Optional) File: resources\trust.jks

If you need a java key store file to connect to your t-vault instance, place the keystore file here, with the name "trust.jks"

#### Running qAPI and testing it
Once all these are configured, you can start running qAPI locally.

You can run the project locally by going to the root folder of the project(qAPI) on your terminal and running:
```
mvn spring-boot:run 
```

Once running locally or hosted somewhere, you can start testing out the qAPI. Below is instructions for how to make a request.

The path of the request
((host-url))/api/datareader/{L1}/{L2}/v1/{env}, ex: localhost:8081/api/datareader/teamA/getusers/v1/prod

- L1 - indicates the name of the config file belonging to a particular customer, as well as the name of the t-vault safe. 

- L2 - the name of the query statement they choose to use from their config file.

- Version - is the version of the API they prefer to use. As of now, there is V1 that gives a helpful message in the response when running into issues. V2 only has the query results in the response.

- Env - (Optional) The folder in their t-vault safe they wish to use. If you have nested folders in the tvault, that is fine. Simply separate the folders with "/".

Additionally, there should be 2 headers to the request.
- Content-Type - application/json
- Authorization - Bearer {tokenValue}

The tokenValue should be the token retrieved from your jhipster registry gateway that you have configured. 

Finally, the body of the request will be passing any dynamic values necessary for the query specified. If no parameter is needed, simply pass in an empty json body.

You should see the result of the query as the response of the request.

For more documentation and instructions for qAPI, please check our [Wiki](https://github.com/tmobile/qapi/wiki)
