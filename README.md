# actiontestscript/linux
## Description
This is a base image for linux based images. It build from actiontestscript/linux-browsers. It contains the following packages:
- ATS java libs
- ATS linuxdriver
- geckodriver
- chromedriver
- edgedriver
- operadriver
- bravedriver
- jasper

## Tags
- `latest` - latest version
- `2.9.5`  - ATS Version 2.9.5 

## Usage
## Windows

<br>

### Settings
To execute .ps1 scripts in PowerShell under Windows, you must modify the ExecutionPolicy settings.
Launch PowerShell in administrator mode and run the following commands:

This command will display the currently set execution policy on your system.

``` powershell
Get-ExecutionPolicy
```

<br>

To set the ExecutionPolicy to `RemoteSigned`, use the following command:
``` powershell
Set-ExecutionPolicy RemoteSigned
```
<br>



After executing all the scripts, it is recommended to revert back to the basic configuration.

``` powershell
Set-ExecutionPolicy Restricted
```

<br>

## Script :

<br>

### Using Docker image in current directories:

- Open the PowerShell console and navigate to the desired directory.
- Map the current directory and grant appropriate permissions.

  <br>

``` powershell
cd <desired directory>
docker run --rm -it -u 0 -v ${PWD}:/home/ats-user/projects actiontestscript/linux:latest sh -c "chown -R 1000:1000 /home/ats-user/projects"
```

- Execute the test with `atsLauncher`.
    - `atsreport`:
        - 1: Simple execution report
        - 2: Detailed execution report
        - 3: Detailed execution report with screenshots
    - `suiteXmlFiles`:
        - List of sequences to launch, separated by commas and without spaces.

<br>

``` powershell
cd <desired directory>
docker run --rm -it -v ${PWD}:/home/ats-user/projects actiontestscript/linux:latest sh -c "java AtsLauncher.java outbound=false atsreport=3 suiteXmlFiles=suite"
```

<br>

### Using a simple git clone and target directorie
  - Create a directory. <br>
  Example: `output`
  - Assign appropriate permissions to the directory.<br>


  ``` powershell
  cd <desired directory>
  mkdir output
  docker run --rm -it -u 0 -v ${PWD}\output:/home/ats-user/outputs actiontestscript/linux:latest sh -c "chown -R 1000:1000 /home/ats-user/outputs"
  ```

<br>

- Clone the test project using standard Git authentication and launch the test suite. <br>
  Example: `ats-test`<br>


    ``` powershell
    docker run --rm -it -v ${PWD}\output:/home/ats-user/outputs actiontestscript/linux:latest sh -c "git clone https://gitlab.com/actiontestscript/ats-test.git . \
    && java AtsLauncher.java output=/home/ats-user/outputs/ats-test outbound=false atsreport=3 suiteXmlFiles=suite "
    ```

<br>

- Or clone the test project using Git token access and launch test suites `suite` and `suite1`. <br>
  Example: `ats-test`<br>

    ``` powershell
    docker run --rm -it -v ${PWD}\output:/home/ats-user/outputs actiontestscript/linux:latest sh -c "git clone https://oauth2:<YOUR_PERSONAL_TOKEN_ACCESS>@gitlab.com/actiontestscript/ats-test.git . \
    && java AtsLauncher.java output=/home/ats-user/outputs/ats-test outbound=false atsreport=3 suiteXmlFiles=suite,suite1 "
    ```

<br>

### Create an image and add git credentials
  - Connect to Git, GitLab, or other platforms using your credentials. Create a read-type access token for the desired repository and obtain your `YOUR_PERSONAL_ACCESS_TOKEN`.

  - Create a credentials file:<br>
  **Save the file without an extension**.<br>
  Example for GitLab:<br>
        
    ``` powershell
    New-Item -Path "${PWD}\gitlab-credential" -ItemType File | Out-Null
    notepad "${PWD}\gitlab-credential"
    ```

    ```
    https://oauth2:<YOUR_PERSONAL_ACCESS_TOKEN>@gitlab.com
    ```
    Save the file with `CTRL+S`

    <br>

  - Create a Docker file<br>
    **Save the file without an extension**.<br>

    ``` powershell
    New-Item -Path "${PWD}\dockerfile_with_credential_git" -ItemType File | Out-Null
    notepad "${PWD}\dockerfile_with_credential_git"
    ```
    ``` docker
    # Dockerfile
    # syntax=docker/dockerfile:1
    FROM actiontestscript/linux:latest
    ARG CREDENTIALS
    RUN git config --global credential.helper store \
      && echo "$CREDENTIALS" > ~/.git-credentials
    ```
    Save the file with `CTRL+S`
    
    <br>


  - Build image
    ``` powershell
    docker build --build-arg CREDENTIALS="$(cat ${PWD}\gitlab-credential)" -f dockerfile_with_credential_git -t linux:credentialGit .
    ```

    <br>

  - Clone the ATS projects repository into the current directory.<br>

    ``` powershell
    cd <desired directory>
    docker run --rm -it -u 0 -v ${PWD}:/home/ats-user/projects linux:credentialGit sh -c "chown -R 1000:1000 /home/ats-user/projects"
    docker run --rm -it -v ${PWD}:/home/ats-user/projects linux:credentialGitsh -c "git clone https://gitlab.com/<url suite> ."
    ```

<br/>

***

## Linux

<br>

## Script :

<br>

### Using Docker image in current directorie:
  - Open the Linux console and navigate to the desired directory.
  - Mount the current directory and ensure appropriate permissions are set.<br>

  ``` sh
  cd <desired directory>
  docker run --rm -it -u 0 -v ./:/home/ats-user/projects actiontestscript/linux:latest sh -c "chown -R 1000:1000 /home/ats-user/projects"
  ```

  - Execute the test with `atsLauncher`.
    - `atsreport`:
        - 1: Simple execution report
        - 2: Detailed execution report
        - 3: Detailed execution report with screenshots
    - `suiteXmlFiles`:
        - List of sequences to launch, separated by commas and without spaces.

  ``` sh
  docker run --rm -it -v ./:/home/ats-user/projects actiontestscript/linux:latest sh -c "java AtsLauncher.java outbound=false atsreport=3 suiteXmlFiles=suite"
  ```

<br>

### Using a simple Git clone and target directory

  - Create a directory. 
  Example: `output`
  - Assign appropriate permissions to the directory.<br>

  ``` sh
  cd <desired directory>
  mkdir output
  docker run --rm -it -u 0 -v ./output:/home/ats-user/outputs actiontestscript/linux:latest sh -c "chown -R 1000:1000 /home/ats-user/outputs"
  ```

<br>

  - Clone the ATS project using standard Git authentication and launch the test suite.
  Example: `ats-test`

<br>

  ``` sh
  docker run --rm -it -v ./output:/home/ats-user/outputs actiontestscript/linux:latest sh -c "git clone https://gitlab.com/actiontestscript/ats-test.git . \
  && java AtsLauncher.java output=/home/ats-user/outputs/ats-test outbound=false atsreport=3 suiteXmlFiles=suite"
  ```

  - Or clone the ATS project using Git token access and launch the `suite` and `suite1` test suites.
  Example: `ats-test`

<br>

  ``` sh
  docker run --rm -it -v ./output:/home/ats-user/outputs actiontestscript/linux:latest sh -c "git clone https://oauth2:<YOUR_PERSONAL_TOKEN_ACCESS>@gitlab.com/actiontestscript/ats-test.git . \
  && java AtsLauncher.java output=/home/ats-user/outputs/ats-test outbound=false atsreport=3 suiteXmlFiles=suite,suite1 "
  ```

<br>

### Create an Image and Add Git Credentials
  - Connect to Git, GitLab, or other platforms using your credentials. Create a read-type access token for the desired repository and obtain your `YOUR_PERSONAL_ACCESS_TOKEN`.

  - Create a credentials file:<br>
  Example for GitLab:

    ``` sh
    cd <desired directory>
    vi gitlab-credential
    ```

    To insert the following line, press `i` and copy and paste it :  <br/>
  
    ``` sh
    https://oauth2:<YOUR_PERSONAL_ACCESS_TOKEN>@gitlab.com
    ```

    To save the file, press `ESC` and then type `:wq` and press `Enter`.
This will save the file and exit the editor.


  - Create a Dockerfile<br>

    ```sh
    vi dockerfile_with_credential_git
    ```
    
    <br>
    To insert the following line, press `i` and copy and paste it :<br>

    ``` docker
    # Dockerfile
    # syntax=docker/dockerfile:1
    FROM actiontestscript/linux:latest
    ARG CREDENTIALS
    RUN git config --global credential.helper store \
      && echo "$CREDENTIALS" > ~/.git-credentials
    ```
    
    <br>
    To save the file, press `ESC` and then type `:wq` and press `Enter`.
    This will save the file and exit the editor.
    
    <br>

  - Build image <br>
  
    ``` sh
    docker build --build-arg CREDENTIALS="$(cat ./gitlab-credential)" -f dockerfile_with_credential_git -t linux:credentialGit .
    ```

    <br>

  - Clone the ATS projects repository into the current directory.<br>

    ``` sh
    cd <desired directory>
    docker run --rm -it -u 0 -v ./:/home/ats-user/projects linux:credentialGit sh -c "chown -R 1000:1000 /home/ats-user/projects"
    docker run --rm -it -v ./:/home/ats-user/projects linux:credentialGitsh -c "git clone https://gitlab.com/<url suite> ."
    ```

<br/>