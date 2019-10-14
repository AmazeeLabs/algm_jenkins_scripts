# Lagoon/Jenkins integration examples

The present repository demonstrates how to interact with a Lagoon instance from within a Jenkins build.

It describes the most basic integration and explains how one can use this functionality within your own build process.
Down the line, if there is a need, we can potentially build the functionality here into a shared Jenkins library.

## Setup

### Set up Jenkins credentials
Ssh credentials are needed for the script to connect to Lagoon and [pull down the JWT](https://lagoon.readthedocs.io/en/latest/using_lagoon/graphql_api/#connect-to-graphql-api) which will subsequently
be used to actually kick off the deployment on the Lagoon instance. Follow the steps described here: [https://jenkins.io/doc/book/using/using-credentials/](https://jenkins.io/doc/book/using/using-credentials/).

Note - the "id" of the credentials is what will be passed as the first argument to the `lagoonDeployEnvironment` function (described below).


### Using the deploy scripts in your Jenkinsfile

The simplest way to (presently) use the scripts here for deployment is to simply copy/paste the `lagoonDeployEnvironment` function
from this repository's Jenkinsfile and paste it into your own pipeline.
The function can then be called from within your pipeline - it takes six arguments (the last three of which are optional, used for self hosted lagoon instances).
These are
1. sshAgentName - the id of the ssh credentials as described above
2. Lagoon project id
3. The Lagoon environment
4. (optional) the lagoon graphql endpoint - defaults to 'https://api.lagoon.amazeeio.cloud/graphql'
5. (optional) the lagoon ssh endpoint - defaults to 'ssh.lagoon.amazeeio.cloud'
6. (optional) the lagoon ssh port - defaults to 32222

It will return a boolean indicating whether the process of _calling_ the deploy was successful (whether the actual deploy is successful is a separate matter).



