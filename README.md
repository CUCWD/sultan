# Sultan

### An Open edX Remote Devstack by Appsembler

This toolkit helps your run devstack on cloud (currently GCP). It will also provide you with the necessary tools to help you using your local machine for development, and your cloud instance for showing and testing.

The ultimate goal of this toolkit is to help you running projects under development on the cloud while having the luxury to run the engineering side on your machine. No dependencies headaches anymore. 

Some available and ready-to-use devstack images are going to help you running the project in a couple of minutes. You can write, delete, and start from scratch anytime with no regrets.

## Documentation
An extensive documentation on the architecture and toolkit can be found in the repo's wiki page [here](https://github.com/appsembler/sultan/wiki).

## Quick Start

First, install the [GCloud SDK](https://cloud.google.com/sdk/gcloud/).

Then pull htis repo and initialize your configuration:

```shell
$ git clone git@github.com:appsembler/sultan.git
$ cd sultan
$ make config.init
## Change SSH_KEY, PROJECT_ID, SERVICE_ACCOUNT_EMAIL, SERVICE_KEY_PATH in the created .configs.<username> file
```
This last command creates a `.config.<username>` file in the root directory. We'll modify it below, but first you'll need to create a service account in the GCloud project you will you for your forthcoming devstack instance (official docs [here](https://cloud.google.com/iam/docs/creating-managing-service-accounts).

Grant the service account permission to create an instance (official docs [here](https://cloud.google.com/iam/docs/granting-roles-to-service-accounts#granting_access_to_a_service_account_for_a_resource). Grant the `roles/compute.instanceAdmin` role. (See also the [list of roles](https://cloud.google.com/sdk/gcloud/reference/iam/roles/list) for more info.)

Follow the official docs ([here](https://cloud.google.com/iam/docs/creating-managing-service-account-keys)) to generate a key for your service account. Store it on your disk and edit the `SERVICE_KEY_PATH` to match its location. 

Modify the following variables in this recently-created config file located at `.config.<username>`: 

* `SSH_KEY`: The private key that has access to the GCP project where we will create your instance
* `PROJECT_ID`: The GCloud project ID.
* `SERVICE_ACCOUNT_EMAIL`: A string that we will use to create a service account. 
* `SERVICE_KEY_PATH`: Path to the service account key created in the steps above. 

If this is your first time creating your instance, we recommend enabling the verbose mode to be able to debug your issues:
* `SHELL_OUTPUT=/dev/null` 
* `VERBOSITY=debug` 

Now, create your instance:

```shell
$ make instance.setup
```

> You need to configure GCloud on your machine before setting the variables above.

To run the devstack
```shell
$ make devstack.run
```

To verify that your devstack is running:
```shell
$ make instance.ping  ## Verifies that your instance is reachable.
$ curl -I edx.devstack.lms:18010  ## Curls your LMS site.
```

To stop the devstack
```shell
$ make devstack.stop
```

To stop the instance server
```shell
$ make instance.stop
```

To remove the instance
```shell
$ make instance.delete
```

> The instance is secured behind a firewall that only identifies your IP. If your IP happened to change (after a network disconnection for example), run `make instance.restrict` to change your IP in the firewall rule.


## Development
There are so many ways you can choose from to interact with the remote code. However, we recommend two common methods to  ensure security, real-time transfer, and immediate reflection on the remote instance:

### SSHFS
To use it all you have to do is to  run `make devstack.mount` and then open your favorite text editor and start editing files on the server from your machine.

### Prefered IDEs
Some IDEs gives you the power to edit code on remote machines. [Visual Studio Code](https://code.visualstudio.com) for example, recently added [Code Remote Extensions](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack). With this extension, you'll be able to open any folder on your remote machine and take advantage of VS Code's full feature set.

## Creating an image
To create an image from your running devstack, use the following
```shell
$ make image.create
```
To create an instance from your image
```shell
$ make instance.setup.image
```

## Environment files
We create a specific ignored .configs file for you run `make config.init`, to debug the final environment variables values you can run
```shell
$ make config.debug
```

## Target help
To check the targets documentation run
```shell
$ make help
```

## Errors
Errors are possible all the time. If an error's fired while executing commands from this toolkit it is recommended to do a little bit more debugging.
While this might be an issue with the tool, we just want you to make sure that have everything correct set up in place:
* Run make config.debug and check if all of your environment variables hold the correct values.
* Toggle the verbosity settings (`VERBOSITY`, and `SHELL_OUTPUT`) in your env file. Follow instructions in the comments above  of them for more details.
* Check our [Wiki](https://github.com/appsembler/sultan/wiki) page for a detailed documentation on the configuration process.

If you couldn't identify the cause of the problem, please submit an issue on https://github.com/appsembler/sultan/issues.
