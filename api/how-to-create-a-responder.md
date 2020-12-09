# How to Write and Submit a Responder

## Table of Contents
  * [Writing a Responder](#writing-an-responder)
    * [The Program](#the-program)
    * [Service Interaction Files (Flavors)](#service-interaction-files-flavors)
    * [Python Requirements](#python-requirements)
    * [Example: Mailer Responder Files](#example-mailer-responder-files)
    * [Input](#input)
    * [Service Interaction Configuration Items](#service-interaction-configuration-items)
    * [Responder Configuration in the Global Configuration File](#responder-configuration-in-the-global-configuration-file)
    * [Output](#output)
    * [The Cortexutils Python Library](#the-cortexutils-python-library)
  * [Submitting a Responder](#submitting-an-responder)
    * [Check Existing Issues](#check-existing-issues)
    * [Open an Issue](#open-an-issue)
    * [Review your Service Interaction File(s)](#review-your-service-interaction-files)
    * [Provide the List of Requirements](#provide-the-list-of-requirements)
    * [Verify Execution](#verify-execution)
    * [Create a Pull Request](#create-a-pull-request)
  * [Need Help?](#need-help)

## Writing a Responder
A responder is a program that takes JSON input and do an action and produces a basic result of that action. Responders are very similar to analyzers though they have different purposes. Responders are made of at least 2 types of files:

- The program itself
- One or several service interaction files or flavors
- A Python requirements file, which is only necessary if the responder is
written in Python.

### The Program
The first type of files a responder is made of is the core program that
performs actions. It can be written in any programming language that is supported by Linux.

you can write your responders in Python, Ruby, Perl or even Scala. However, the very handy `Cortexutils` library [described below](#the-cortexutils-python-library) is in Python. It greatly facilitates responder development and
it also provides some methods to quickly format the output to make it compliant
with the JSON schema expected by [TheHive](https://github.com/TheHive-Project/TheHive/).

### Service Interaction Files (Flavors)
A responder must have at least one service interaction file. Such files
contain key configuration information such as the responder's author
information, the datatypes (`thehive:case`, `thehive:alert`, ...) the responder accepts as
input and to which it applies to if used from TheHive, the TLP and PAP (or [Permissible Actions Protocol](https://www.misp-project.org/taxonomies.html#_pap)) above which it will refuse to execute to protect against data
leakage and to enforce sane OPSEC practices and so on.

A responder can have two or more service interaction files to allow it to
perform different actions.  We speak then of flavors. For example, a Mailer
responder can send message using several body templates.

### Python Requirements
If the responder is written in Python, a `requirements.txt` must be provided
with the list of all the dependencies.

### Example: Mailer Responder Files
Below is a directory listing of the files corresponding to a Mailer
responder.

```bash
responders/Mailer
|-- Mailer.json
|-- requirements.txt
`-- mailer.py
```

### Input
The input of a responder can be any JSON data, even a simple string. The submitter must send data with
the structure expected by the program. The acceptable datatypes described in the
Service Interaction files indicate what kind of data is expected.
For example, if the program requires a `thehive:case` (i.e. it applies at the case level in TheHive), input must comply with TheHive case. Below an example of `thehive:case` input.

```json
	{
	    "data": {
		"updatedAt": 1606230814019,
		"tlp": 2,
		"endDate": 1606230814019,
		"description": "Case Description",
		"tags": [
		    "tag"
		],
		"caseId": 157,
		"customFields": {},
		"pap": 2,
		"status": "Open",
		"resolutionStatus": "Indeterminate",
		"createdAt": 1606183201646,
		"createdBy": "user",
		"flag": false,
		"severity": 2,
		"metrics": {},
		"owner": "user",
		"title": "Title",
		"updatedBy": "user",
		"startDate": 1606183201000,
		"impactStatus": "NotApplicable",
		"_type": "case",
		"_routing": "iwD693UBlJefU8pMrqOq",
		"_parent": null,
		"_id": "iwD693UBlJefU8pMrqOq",
		"_seqNo": 4572,
		"_primaryTerm": 48,
		"id": "iwD693UBlJefU8pMrqOq"
	    },
	    "dataType": "thehive:case",
	    "tlp": 2,
	    "pap": 2,
	    "message": "",
	    "parameters": {
		"user": "user"
	    },
	    "config": {
		"proxy_https": null,
		"cacerts": null,
		"max_pap": 2,
		"jobTimeout": 30,
		"api_key": "3bDBUb7EL409MHOmXBkqsysZ1vpTab1Q",
		"check_tlp": true,
		"proxy_http": null,
		"max_tlp": 2,
		"url": "configured_url",
		"check_pap": true
	    }
	}
```

In the addition to the input (`data` section) sent by the submitter, Cortex adds the `config` section which is the responder's specific configuration provided by an `orgAdmin` user when the responder is enabled in the Cortex UI. 

#### Example: Service Interaction File for the Mailer Responder
The `<==` sign and anything after it are comments that do no appear in the
original file.
```json
{
  "name": "Mailer",
  "version": "1.0",
  "author": "CERT-BDF",
  "url": "https://github.com/TheHive-Project/Cortex-Analyzers",
  "license": "AGPL-V3",
  "description": "Send an email with information from a TheHive case or alert",
  "dataTypeList": ["thehive:case", "thehive:alert", "thehive:case_task"],
  "command": "Mailer/mailer.py",
  "baseConfig": "Mailer",
  "configurationItems": [
    {
      "name": "from",
      "description": "email address from which the mail is send",
      "type": "string", <== defines what kind of data type the configuration item is (string, number)
      "multi": false, <== setting multi to true allows to pass a list of items
      "required": true
    },
    {
      "name": "smtp_host",
      "description": "SMTP server used to send mail",
      "type": "string",
      "multi": false,
      "required": true,
      "defaultValue": "localhost"
    },
    {
      "name": "smtp_port",
      "description": "SMTP server port",
      "type": "number",
      "multi": false,
      "required": true,
      "defaultValue": 25
    },
    {
      "name": "smtp_user",
      "description": "SMTP server user",
      "type": "string",
      "multi": false,
      "required": false,
      "defaultValue": "user"
    },
    {
      "name": "smtp_pwd",
      "description": "SMTP server password",
      "type": "string",
      "multi": false,
      "required": false,
      "defaultValue": "pwd"
    }
  ]
}
```

### Service Interaction Configuration Items
#### name
Name of the specific service (or flavor) of the responder.

If your responder has only one service interaction (i.e. performs only one
action), it is the name of the responder's directory.

If your responder performs several actions (i.e. comes in several flavors),
you have to give a specific and meaningful name to each flavor.

Each flavor's name appear in TheHive's responder list and in MISP when you
use Cortex for attribute enrichment.

#### version
The version of the responder.

You **must** increase major version numbers when new features are added,
modifications are made to take into account API changes, report output is
modified or when report templates (more on this later) are updated.

You must increase minor version numbers when bugs are fixed.

#### author
You must provide your full name and/or your organization/team name when
submitting a responder. Pseudos are not accepted. If you'd rather remain
anonymous, please contact us at support@thehive-project.org prior to
submitting your responder.

#### url
The URL where the responder is stored. This should ideally be
`https://github.com/TheHive-Project/Cortex-Analyzers`

#### license
The license of the code. Ideally, we recommend using the AGPL-v3
license.

Make sure your code's license is compatible with the license(s) of the
various components and libraries you use if applicable.

#### description
Description of the responder. Please be concise and clear. The description is
 shown in the Cortex UI and TheHive.

#### dataTypeList
The list of TheHive datatypes supported by the responder. Currently TheHive
accepts the following datatypes:

- `thehive:case`
- `thehive:case_artifact` (i.e. observable)
- `thehive:alert`
- `thehive:case_task`
- `thehive:case_task_log` (i.e. task log)

#### baseConfig
Name used to group configuration items common to several responders. This
prevent the user to enter the same API key for all responder flavors.
The Cortex responder config page group configuration items by their `baseConfig`.

#### config
Configuration dedicated to the responder's flavor. This is where we
 typically specify the TLP level of observables allowed to be analyzed with the
 `check_tlp` and `max_tlp` parameters. For example, if `max_tlp` is set to `2` (TLP:AMBER), TLP:RED observables cannot be analyzed.

#####  max_tlp
The TLP level above which the responder must not be executed.

| TLP   |     max_tlp value     |
|:----------:|:-------------:|
| Unknown |  -1 |
| WHITE |   0  |
| GREEN | 1 |
| AMBER | 2 |
| RED | 3 |


##### check_tlp
This is a boolean parameter. When `true`, `max_tlp` is checked. And if the
input's TLP is above `max_tlp`, the responder is not executed.

For consistency reasons, we do recommend setting both `check_tlp` and
`max_tlp` even if `check_tlp` is set to `false`.

#####  max_pap
The PAP level above which the responder must not be executed.

| TLP   |     max_tlp value     |
|:----------:|:-------------:|
| Unknown |  -1 |
| WHITE |   0  |
| GREEN | 1 |
| AMBER | 2 |
| RED | 3 |


##### check_pap
This is a boolean parameter. When `true`, `max_pap` is checked. And if the
input's PAP is above `max_pap`, the responder is not executed.

For consistency reasons, we do recommend setting both `check_pap` and
`max_pap` even if `check_pap` is set to `false`.

##### command
The command used to run the responder. That's typically the full, absolute
path to the main program file.

#### configurationItems
The list of configurationItems is necessary in order to be able to set all configuration variables for responders directly in the Cortex 2 user interface. As in the VirusTotal example above can be seen, every item is a json object that defines:
- name (string)
- description (string)
- type (string)
- multi (boolean)
- required (boolean)
- defaultValue (according to type, optional)

The `multi` parameter allows to pass a list as configuration variable instead of a single string or number. This is used e.g. in the MISP responder that queries multiple servers in one run and needs different parameters for that.

### Output
The output of a responder depends on the success or failure of its execution.

If the responder **fails** to execute:

```json
{
    "success": false,
    "errorMessage":".."
}
```

-   When `success` is set to `false`, it indicates that something went wrong
    during the execution.
-   `errorMessage` is free text - typically the error output message.

If the responder **succeeds** (i.e. it runs without any error):

```json
{
    "success":true,
    "full":{ "message": ".." },
    "operations":[]
}
```

-   When `success` is set to `true`, it indicates that the responder ran
    successfully.
-   `full` is the full report of the responder. It must contain at least
    a message.
-   `operations` is a list what the submitter system should execute.
    As of version 3.1.0, TheHive accepts the following operations:
    -    `AddTagToArtifact` (`{ "type": "AddTagToArtifact", "tag": "tag to add" }`): add
         a tag to the artifact related to the object
    -    `AddTagToCase` (`{ "type": "AddTagToCase", "tag": "tag to add" }`): add
         a tag to the case related to the object
    -    `MarkAlertAsRead`: mark the alert related to the object as read
    -    `AddCustomFields` (`{"name": "key", "value": "value", "tpe": "type"`): add a custom field to the case related to the object
   
  The list of acceptable operations will increase in future releases of TheHive.

### The Cortexutils Python Library
So far, all the published responders have been written in Python. We provide a Python library called `cortexutils` to help developers easily write their programs. Note though that Python is not mandatory for responder coding and any language that runs on Linux can be used, though you won't have the benefits of the CortexUtils library.

Cortexutils can be used with Python 2 and 3. Due to the end of life from Python2 it is strongly advised to work as much with Python3 as possible.
To install it :

```bash
pip install cortexutils
```

or

```bash
pip3 install cortexutils
```

This library is already used by all the responders published in our [Github
repository](https://github.com/TheHive-Project/Cortex-Analyzers). Feel free to start reading the code of some of them before writing your own.

## Submitting a Responder
We **highly encourage you to share your responders** with the community through our Github repository. To do so, we invite you to follow a few steps before submitting a pull request.

### Check Existing Issues
Start by checking [if an issue already exists](https://github.com/TheHive-Project/Cortex-Analyzers/issues?utf8=%E2%9C%93&q=is%3Aissue%20is%3Aopen%20label%3A%22feature%20request%22%20label%3Aresponder) for the responder you'd like to write and contribute. Verify that nobody is working on it. If an issue exists and has the **in progress**, **under review** or **pr-submitted** label, it means somebody is already working on the code or has finished it.

If you are short on ideas, check issues with a [**help wanted** label](https://github.com/TheHive-Project/Cortex-Analyzers/issues?utf8=%E2%9C%93&q=is%3Aissue%20is%3Aopen%20label%3A%22help%20wanted%22). If one of those issues interest you, indicate that you are working on it.

### Open an Issue
If there's no issue open for the responder you'd like to contribute, [open one](https://github.com/TheHive-Project/Cortex-Analyzers/issues/new). Indicate that you are working on it to avoid having someone start coding it.

You have to create an issue for each responder you'd like to submit.

### Review your Service Interaction File(s)
Review your service interaction files. For example, let's check the
Mailer JSON responder configuration file(s):

```json
{
  "name": "Mailer",
  "version": "1.0",
  "author": "CERT-BDF",
  "url": "https://github.com/TheHive-Project/Cortex-Analyzers",
  "license": "AGPL-V3",
  "description": "Send an email with information from a TheHive case or alert",
  "dataTypeList": ["thehive:case", "thehive:alert", "thehive:case_task"],
  "command": "Mailer/mailer.py",
  "baseConfig": "Mailer",
  "configurationItems": [
    {
      "name": "from",
      "description": "email address from which the mail is send",
      "type": "string", <== defines what kind of data type the configuration item is (string, number)
      "multi": false, <== setting multi to true allows to pass a list of items
      "required": true
    },
    {
      "name": "smtp_host",
      "description": "SMTP server used to send mail",
      "type": "string",
      "multi": false,
      "required": true,
      "defaultValue": "localhost"
    },
    {
      "name": "smtp_port",
      "description": "SMTP server port",
      "type": "number",
      "multi": false,
      "required": true,
      "defaultValue": 25
    },
    {
      "name": "smtp_user",
      "description": "SMTP server user",
      "type": "string",
      "multi": false,
      "required": false,
      "defaultValue": "user"
    },
    {
      "name": "smtp_pwd",
      "description": "SMTP server password",
      "type": "string",
      "multi": false,
      "required": false,
      "defaultValue": "pwd"
    }
  ]
}

```
Ensure that all information is correct and particularly the `author` and
`license` parameters.

### Provide the List of Requirements
If your responder is written in Python, make sure to complete the
`requirements.txt` file with the list of all the external libraries that are
needed to run the responder correctly.

### Verify Execution
Use these three simple checks before submitting your responder:

-   Ensure it works with the expected configuration, TLP, PAP or datatype.
-   Ensure it works with missing configuration, PAP, datatype or TLP: your
responder must generate an explicit error message.

### Create a Pull Request
Create one Pull Request per responder against the **develop** branch of the
[Cortex-Analyzers](https://github.com/TheHive-Project/Cortex-Analyzers/) repository. Reference the issue you've created in your PR.

We have to review your responders. Distinct PRs will allow us to review them
more quickly and release them to the benefit of the whole community.

## Need Help?
Something does not work as expected? No worries, we got you covered. Please
join our [user forum](https://groups.google.com/a/thehive-project.org/forum/#!forum/users),
 contact us on [Gitter](https://gitter.im/TheHive-Project/TheHive), or send us
 an email at [support@thehive-project.org](mailto:support@thehive-project
 .org). We are here to help.
