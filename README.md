
# EC Accelerator
 
  
이미지 생성 :

Step 1. git clone https://github.com/kin3303/ecea-docker.git

Step 2. build.sh 파일을 이용하여 도커 이미지를 빌드

Examples :

RedHat 이미지 빌드

```console
   $ cd ecea-docker/build ;
   $ ./build.sh -c=/tmp/test -t=agent -s=rh
```

Ubuntu 이미지 빌드

```bash
   cd ecea-docker/build ;
  ./build.sh -c=/tmp/test -t=agent -s=ubuntu
```

CentOS 이미지 빌드

```bash
   cd ecea-docker/build ;
  ./build.sh -c=/tmp/test -t=agent -s=centos
```

Step 3. Run the ECFS installer on the host machine where docker container with agents will be started (Ask ElectricCloud for ECFS installer)

Step 4. Start up Docker image
Example for starting agents :

```bash
   docker run --privileged=true \
   -i -d -t \
   -e CMHOST=10.200.1.97 \
   -e AGENT_RESOURCE=linux  \
   --device /dev/efs \
   --net=host \
   --name=ec_agent \
   agent_11.0_rh_alpha
```
where :

 -e CMHOST=10.200.1.97  - IP Address of Cluster Manager host for agents to connect, default is localhost
 
 -e AGENT_NUMBER=2      - number of agents to start

## USAGE
```
    "Usage: ./build.sh -t=<build_target> -c=<content_folder> -s=<system_name> [-v=<build_version>]"
    "1 -t=*| --target=*          : <build_target>   - agent | cm | emake"
    "2 -v=*| --version=*         : <build_version>  - in format like 10.0 - optional"
    "3 -c=*| --content_folder=*  : <content_folder> - build folder to prepare content for acceletor-target docker image and build image from it"
    "4 -s=*| --system=*          : <system_name>    - rh | centos | ubuntu" 
    "5 -r  | --reuse - tell to the build image  process to reuse tar archive (if it was prepared earlier) instead of creating new one - optional" 
    "6 -o  | --onlytar - tar from /opt/ecloud to the tarball with name ecloud.tar.gz and exit
             should be used with flags : --target, --content_folder --system
    "
    "7 -h  | --help  - print help" 
```

## To create only archive for further generation of docker image

If you need to create 'ecloud.tar.gz' only - there is an option '-o' or '--onlytar'
(you can use created archive with option '--reuse' in future on any host)
To create 'ecloud.tar.gz':

- Be sure that you done with installation files on a machine (Step 1.)

- Run build.sh with --onlytar option

Example for agents on RedHat : 
```bash
  CONTENT_FOLDER=/tmp/test
   ./build.sh -t=agent \
   -c=$CONTENT_FOLDER \
   -s=rh \
   --onlytar
```
as a condition  --onlytar | -o - options should be used with flags -t, -c, -s 

- As a result build process will be printed (to the stdout) path of created 'ecloud.tar.gz'

## Reuse of ecloud.tar.gz archive

To reuse ecloud.tar.gz on a another machines for building docker image (no need to install again Agents or ClusterManager or Emake on a machine) user can use option -r or --reuse

To use -r option:

- Create <content_folder> folder on your new machine, eg. /tmp/test

- Create target folder in it, eg. <content_folder>/agent

- Copy ecloud.tar.gz from your build machine to the target folder, eg. /tmp/test/agent

- Run build.sh

Example :

```bash 
   CONTENT_FOLDER=/tmp/test
   
   mkdir -r $CONTENT_FOLDER/agent 
   
   cp ../from/ecloud.tar.gz  to $CONTENT_FOLDER/agent 
   
   cd ecea-docker/build && \
   ./build.sh -t=agent \
   -c=$CONTENT_FOLDER \
   -s=rh \
   -r 
```

  

