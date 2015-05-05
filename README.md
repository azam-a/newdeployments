[![Build Status](https://travis-ci.org/azam-a/newdeployments.svg?branch=master)](https://travis-ci.org/azam-a/newdeployments) [![Coverage Status](https://coveralls.io/repos/azam-a/newdeployments/badge.svg?branch=master)](https://coveralls.io/r/azam-a/newdeployments?branch=master) [![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/azam-a/newdeployments/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/azam-a/newdeployments/?branch=master)

# newdeployments

A static file (CSS, JS) uploader to AWS S3, with versioning capability

## Background

In continuous deployment environment for web applications, mismatches between application templates (HTML) and static files may result in applications with distorted views and borked functionalities. In order to minimise the risk of application not synchronised with its static files during deployment, a versioning strategy has been devised.

## Current Features / Behaviours

- Developed in context of another application repository, and Jenkins as the CI tool
- An index file (in XML) is to be maintained in the application repository, containing references to static files and their latest versions
- Minification of the static files using [YUI Compressor](http://yui.github.io/yuicompressor/) (CSS) and [Closure Compiler](https://developers.google.com/closure/compiler/) (JS)
- Compression of the minified files, into GZIP format
- Upload to AWS S3, with versioned name, using Boto (AWS SDK)


## Why X, Y, Z?

- Windows - developed and tested on Windows. The development and Jenkins machines are Windows. Untested on other platforms
- Why open-source this? - Some poor souls in alternate universes may have the same need and actually thought that this solution would be optimal


## Setup steps (into machine running Jenkins):

1. Install Python 3.4, pip, virtualenv (optional) and add paths accordingly to system environments

2. In activated virtualenv session (or global environment), install required packages from requirements.txt

    ```
    pip install -r requirements.txt
    ```

3. Create a Jenkins job and set the application source repository containing XML index and static files accordingly

4. Copy content of this repository anywhere in the system (preferably into a directory at the same level of the job's workspace folder)

5. Add a build step in the job to Execute Windows batch command, calling mydeploy.py script from workspace context

    ```
    C:\Python34\python.exe "%WORKSPACE%/../newdeployments/mydeploy.py"
    ```

6. Modify the PATH constants at the top of mydeploy.py script to suit the context of the environment
