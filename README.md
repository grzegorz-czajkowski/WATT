![](https://github.com/Samsung/WATT/blob/master/public/image/watt.png)
# WATT (WebAssembly Translation Toolkit)
[![License](https://img.shields.io/badge/licence-Apache%202.0-brightgreen.svg?style=flat)](LICENSE)
[![Build Status](https://travis-ci.org/Samsung/WATT.svg?branch=master)](https://travis-ci.org/Samsung/WATT)

WATT is server-based WebAssembly IDE.

If you want to contribute code, please check the [contribution guidelines](https://github.com/Samsung/WATT/blob/master/CONTRIBUTING.md).

## Prerequisites
* Install [MongoDB](https://www.mongodb.com/download-center?jmp=nav#community)

### Optional dependencies
1. Tizen SDK for building .wgt packages
    * Install [Tizen SDK](https://developer.tizen.org/development/tizen-studio/download)
    * Install Tizen SDK Native CLI development packages
        * For IDE Tizen SDK installer use Tizen Package Manager GUI and install `Native CLI` from `Tizen SDK tools`
        * For CLI Tizen SDK installer use `package-manager-cli.bin` in `TIZEN_SDK_PATH/package-manager`
        ```bash
        ./package-manager-cli.bin install NativeCLI
        ```
    * Add `tizen` CLI-tool to the system PATH in the terminal where you run WATT
    ```bash
    export PATH=$PATH:TIZEN_SDK_PATH/tools/ide/bin/
    ```

## Quick Start
* Getting the sources:
```bash
git clone https://github.com/Samsung/WATT.git
cd WATT
```

* Start the server:

WATT will run some internal executable files,

for this, WATT needs set paths of executable files on System Environment Variable(PATH).


If you don't start the server with launch, WATT could not provide full functionality.
```bash
./launch
```

## Tests
To run the test suite, first install the dependencies, then run npm test:
```bash
npm install
npm test
```

* Connect to the web server, the service is provided with port number 3000:
```bash
On browser, http://localhost:3000/
```

## Developing Design Editor in WATT
* Design Editor is located in libs/tau-wysiwig
* After making DE changes launch WATT with bp option
```bash
./launch -bp
```
* After making changes on already running WATT:
```bash
# in WATT directory
cd libs/tau-wysiwig
npm install
npm run-script build-watt
```
* Always after making any DE changes in WATT console:
```bash
b
```

## Running WATT in docker container
 * Install docker-ce (not docker). Follow steps from https://docs.docker.com/install/linux/docker-ce/ubuntu/. Recommended version is 18.06.1~ce~3-0~ubuntu. If you are using a newer one give it a try.
 * Install docker compose. Follow steps from https://docs.docker.com/compose/install/.
 * Verify docker installation:
```bash
docker run hello-world
```
 * If the following error appears this means you have to [add your user to docker group](https://docs.docker.com/install/linux/linux-postinstall/).
```bash
ERROR: Couldn't connect to Docker daemon at http+docker://localunixsocket - is it running?
```
 * If user can not be added due to the following error, manually add your user to docker group, for example, using *sudo vigr* tool.
```bash
usermod: user 'foo' does not exist
```
* WATT along with mongodb images can be built and run by:
```bash
./docker-run.sh
```

* This pulls and builds necessary images if they do not exists on local machine. Otherwise, docker tries to use existing images even if they are out of dated. Add '--rebuild' paramter in order to create new image.

* Ensure 3000 port to be free. You should see logs on terminal:
```bash
watt_container_1   | Listening on port 3000
watt_container_1   | TAUComm started
mongo_container_1  | 2019-03-11T07:18:23.178+0000 I NETWORK  [thread1] connection accepted from 172.19.0.3:36916 #1 (1 connection now open)
```
* Open localhost:3000 in Browser.

## Inspecting docker images.
WATT and mongodb images are composed together by docker-compose.yml. However, if you want to execute any command on particular image type:
```bash
docker run -it 5f142ecd12f5 bash
```
ImageId can be found from:
```bash
docker images
```
## Building WATT in docker container
If you already did compose, for example, by invoking ./docker-run.sh you can attach to running container:
```bash
docker container attach 12cf98736487
```
And type 'b', container id comes from
```bash
docker container ls
```

## Inspecting running container
It's possible to execute any command on running container:
```bash
docker container exec -i 12cf98736487 bash
```

## Updating WATT in AWS instance
 * Make sure your .git folder is not huge since its size significantly increases docker image.
 * [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html)
 * Build and push watt docker image to AWS repositories and finally restart all WATT instances by
```bash
./watt-aws-instances-update.sh AWS_ACCOUNT_ID [--simultaneous-image-push]
```
 * If you need additional steeps, for example, updating mongod image or create new repository just follow [this guide.](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html#use-ecr)
 * Images should be available at [repositories](https://ap-northeast-2.console.aws.amazon.com/ecr/repositories?region=ap-northeast-2#).
 * Running task should be available [here](https://ap-northeast-2.console.aws.amazon.com/ecs/home?region=ap-northeast-2#/clusters/watt-cluster/tasks)
 * Verify if WATT starts properly by inspecting the logs.
 * Check instance by visiting [TAU checkbox sample](https://code.tizen.org/demos?path=1.0%2Fexamples%2Fmobile%2FUIComponents%2Fcomponents%2Fcontrols%2Fcheckbox.html).


## WATT instances on AWS
 * [ap-northeast-2 Seoul](http://54.180.160.96:3000), [checkbox example](http://54.180.160.96:3000/demos?path=1.0%2Fexamples%2Fmobile%2FUIComponents%2Fcomponents%2Fcontrols%2Fcheckbox.html).
 * [ap-south-1 Mumbai](http://13.233.41.131:3000), [checkbox example](http://13.233.41.131:3000/demos?path=1.0%2Fexamples%2Fmobile%2FUIComponents%2Fcomponents%2Fcontrols%2Fcheckbox.html).


## Creating new AWS instance in desired AWS region
 * [Install the Amazon ECS CLI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI_installation.html).
 * [Switch AWS Web Console](https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-2#Instances:sort=instanceId) to region to which you want to set up new instance (top right corner, next to Support).
 * Create network infrastructure:
    * Define [VPC with Elastic IP](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/create-public-private-vpc.html).
    * Define *security group* using previously created *VPC*.
    * Add 22 and 3000 ports to *Inboud Roules* of newly created security group.
 * [Create Your Cluster](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cli-tutorial-ec2.html), for example, 
```bash
ecs-cli up --force --keypair id_rsa --capability-iam --size 1 --instance-type t2.large --vpc vpc-0d05d256d9261ccb5 --subnets subnet-0b31dfed2f9dddb0a --security-group sg-09d2b747ca8b77f1a --cluster-config watt-cluster-config --region REGION_CODE
```
 * To find subnet id go to [Subnet dashboard](https://ap-northeast-2.console.aws.amazon.com/vpc/home?region=ap-northeast-2#subnets:sort=SubnetId) change the region and copy public subnet id associated with newly created VPC.
 * Get WATT and mongod images using the following script:
```bash
./docker-run.sh --rebuild
```
 * You don't have to necessarily wait for complete WATT set up in docker environment. It's enough to have required images that can be checked by:
```bash
docker images
```
 * Visit [docker repositories](https://ap-northeast-2.console.aws.amazon.com/ecr/repositories?region=ap-northeast-2#), change the region and create repositories for WATT and mongod images.
 * Update docker-compose-aws.yml with new docker images repositories and awslogs-region.
 * You can change default memory limits for each container in ecs-params.yml
 * [Deploy the Compose File to a Cluster](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-cli-tutorial-ec2.html#ECS_CLI_tutorial_compose_deploy), for example,
```bash
ecs-cli compose --file docker-compose-aws.yml --verbose up --create-log-groups --cluster-config watt-cluster-config --region REGION_CODE
```
 * [Stop current task](https://ap-northeast-2.console.aws.amazon.com/ecs/home?region=ap-northeast-2#/clusters/watt-cluster/tasks) If container can not be run due to the following error:
```bash
INFO[0003] Couldn't run containers                       reason="RESOURCE:MEMORY"
```
 * See watt-awslogs-group at [CloudWatch](https://us-east-2.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-2#logs), change the region
 * Logs can be also download them by
```bash
aws logs get-log-events --log-group-name watt-awslogs-group --log-stream-name watt/watt_container/ID --output text --region REGION_CODE
```
 * If you see 'Invalid command () was entered' please follow further steps
 * Due to no support for [interactive mode in compose](https://github.com/aws/amazon-ecs-cli/issues/706) there is a need to manually edit task definition
 * Go WATT Task [Definition](https://ap-northeast-2.console.aws.amazon.com/ecs/home?region=ap-northeast-2#/taskDefinitions/WATT) and change the region.
 * Click "Create new revision".
 * At the bottom, click on "Configure via JSON" button and replace *null* to *true* for the following properties in watt (not mongodb) container:
```bash
"interactive": true,
"pseudoTerminal": true,
```
 * Click "Create" button.
 * Click "Actions" button and select "Run task".
 * Select "watt-cluster" from Cluster menu and click "Run Task" button.
 * Verify logs if WATT was started.
 * Go to "ECS Instances" tab in [custer view](https://ap-south-1.console.aws.amazon.com/ecs/home?region=ap-south-1#/clusters/watt-cluster/tasks).
 * Change region.
 * Click on "ECS Instance", for example, i-03e55f244e70c32f
 * Copy instance ip.
 * Verify this instance by visiting demo page: http://INSTANCEIP:3000/demos?path=1.0%2Fexamples%2Fmobile%2FUIComponents%2Fcomponents%2Fcontrols%2Fcheckbox.html

## License
Refer [WATT License](https://github.com/Samsung/WATT/wiki/License)
