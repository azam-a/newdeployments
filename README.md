[![Build Status](https://travis-ci.org/azam-a/newdeployments.svg?branch=master)](https://travis-ci.org/azam-a/newdeployments) [![Coverage Status](https://coveralls.io/repos/azam-a/newdeployments/badge.svg?branch=master)](https://coveralls.io/r/azam-a/newdeployments?branch=master) [![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/azam-a/newdeployments/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/azam-a/newdeployments/?branch=master)

# newdeployments

A static file (CSS, JS, image) uploader to AWS S3, with versioning capability

## Background

In continuous deployment environment for web applications, mismatches between application templates (HTML) and static files may result in applications with distorted views and borked functionalities. In order to minimise the risk of application not synchronised with its static files during deployment, a versioning strategy has been devised.

## Current Features / Behaviours

- Developed in context of another application repository, and Jenkins as the CI tool
- An index file (in XML) is to be maintained in the application repository, containing references to static files and their latest versions
- Minification of the static files using [YUI Compressor](http://yui.github.io/yuicompressor/) (CSS) and [Closure Compiler](https://developers.google.com/closure/compiler/) (JS)
- Also supports image files (without minification)
- Compression of the minified files, into GZIP format
- Upload to AWS S3, with versioned name, using `boto` ([github](https://github.com/boto/boto))
- Cleanup of old versioned static files from their buckets

## Normal Use Case

![Overall diagram](https://github.com/azam-a/newdeployments/blob/master/usecase.png)

1. Developers save static files in `repo/css/`, `repo/scripts/` and `repo/images/` folders
 * Index of the files are generated by developers and saved in fileVersion.xml [(example)](https://github.com/azam-a/newdeployments/blob/master/fixtures/end_to_end/config/mydeploy.xml)
 * All the changes (css, scripts, images and XML index) are committed into repository
2. Jenkins will checkout latest commit from repo and executes `mydeploy.py`
3. The script checks if the indexed files in XML are already in S3 buckets, and skips already-existing versions from processing
4. The script will minify and compress the files indexed in XML from the checked out repo
5. The compressed files are uploaded to their respective S3 buckets, with their version appended to the filename
6. Web application will automatically append versions to the static file references based on the XML file (out of scope of this repo)
7. Users will receive references to exact versions of static files via web servers
8. For every future modifications to the tracked static files, their versions in the XML file are to be modified by developer and committed into the repo
 * This script will again take care of the versioning for new deployments when trigerred via Jenkins
 * This workflow ensures that the deployed web application will always request for the latest version of the static files, based on the XML index file
 * To clean up old versioned files in the buckets, a cleanup script (`mycleanup.py`) can be executed, whilst preserving currently-indexed files in the XML

## Why X, Y, Z?

- Windows - developed and tested on Windows. The development and Jenkins machines are Windows. Untested on other platforms
- Why open-source this? - Some poor souls in alternate universes may have the same need and actually thought that this solution would be optimal
- Cleanup for previous outdated file versions on the S3 buckets <del>is yet to be implemented (to-do)</del> has been [implemented](https://github.com/azam-a/newdeployments/blob/master/mycleanup.py)

## Setup steps (into machine running Jenkins):

1. Install `Python 3.4` and `virtualenv` (optional), then add paths accordingly to system environments

2. In activated virtualenv session (or global environment), install required packages from `requirements.txt`:

    ```
    pip install -r requirements.txt
    ```

3. Create a Jenkins job and set the application source repository containing XML index and static files accordingly

4. Copy content of this repository and put it anywhere in the system 

5. Add a build step in the job to Execute Windows batch command, calling `mydeploy.py` script from workspace context or absolute path:

    ```
    C:\Python34\python.exe "%WORKSPACE%/../newdeployments/mydeploy.py"
    ```

    OR

    ```
    C:\Python34\python.exe "C:\scripts\newdeployments\mydeploy.py"
    ```

6. Repeat Steps 3 & 5 for cleanup script, substituting `mydeploy.py` with `mycleanup.py`

7. Modify the PATH constants in `environment_config.py` ([template](https://github.com/azam-a/newdeployments/blob/master/environment_config.py)) to suit the context of the environment
