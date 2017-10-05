**DRAFT -- NOT READY**

How to write and submit an analyzer
===================================

What is an analyzer ?
---------------------

An analyzer can be seen as a program that simply takes an observable and
configuration information as **raw input** and gives back a result as
**raw output**.

Technically, an analyzer is 2 or 3 types of files :

``` {.sourceCode .bash}
analyzers/VirusTotal
├── VirusTotal_GetReport.json
├── VirusTotal_Scan.json
├── requirements.txt
├── virustotal.py
└── virustotal_api.py
```

-   **\[REQUIRED\]** the program itself : python or any other language
    program that runs on Linux (`*.py` files)
-   **\[REQUIRED\]** Service files (`*.json` files) : an analyzer can
    manage many services so you can have a unique program that manages
    to run different actions. For example, to send a file to VirusTotal
    or *only* get the last report of its hash. This file may contain
    specific configuration for the service. For VT analyzer, by default
    we chose to send files with **TLP WHITE** or **GREEN** only
-   `requirements.txt` contains all dependencies references for python
    programs - required if the analyzer is written with Python.

### Input

The input is a JSON formatted as follow :

``` {.sourceCode .json}
{
    "data":"d41d8cd98f00b204e9800998ecf8427e",
    "dataType":"hash",
    "tlp":0,
    "config":{
        "key":"1234567890abcdef",
        "max_tlp":3,
        "check_tlp":true,
        "service":"GetReport"
        [..]
    },
    "proxy":{
        "http":"http://myproxy:8080",
        "https":"https://myproxy:8080"
      }
  }
```

`data`, `dataType` and `tlp` are the observable information coming from
TheHive or whatever program that is using Cortex. config is the
analyzer's specific set of configuration coming from the configuration
file - `/etc/cortex/application.conf` and the service configuration
file, `VirusTotal_GetReport.json` for example.

As an example, let's take the *GetReport* service of the VirusTotal
analyzer:

1.  Observable

``` {.sourceCode .json}
{
    "data":"d41d8cd98f00b204e9800998ecf8427e",
    "dataType":"hash",
    "tlp":0,
    [..]
  }
```

2.  Configuration of the VirusTotal GetReport service

``` {.sourceCode .json}
{
    "name": "VirusTotal_GetReport",
    "version": "3.0",
    "author": "CERT-BDF",
    "url": "https://github.com/CERT-BDF/Cortex-Analyzers",
    "license": "AGPL-V3",
    "description": "Get the latest VirusTotal report for a file, hash, domain or an IP address",
    "dataTypeList": ["file", "hash", "domain", "ip"],
    "baseConfig": "VirusTotal", <== name of base config in /etc/cortex/application.conf
    "config": {
        "check_tlp": true,
        "max_tlp": 3,
        "service": "get"
    },
    "command": "VirusTotal/virustotal.py" <= main program
}
```

name

:   Name of the specific service of the analyzer. If your analyzer is
    dealing with only one service, this is the name of the analyzers,
    otherwise, you have to set a specific and meaninful name. This is
    the name that appear in TheHive in analyzers list.

version

:   version of the analyzer. Major version bumps when new features are
    added, api changes, report output is modified and report templates
    is updated. Minor version bumps when bugs are fixed. The version
    number is also used in the folder name of associated report
    templates ; e.g. *VirusTotal\_GetReport* and *3.0* on the JSON file
    should meet a folder named *VirusTotal\_GetReport\_3\_0* for
    report templates.

author

:   When submitting an analyzer please fill the author field.

url

:   url where the analyzer is stored

license

:   License of the code. Please make sure that the license you choose is
    compatible with en AGPL-V3 license of Cortex and TheHive.

description

:   description of the analyzer. It is shown into TheHive.

dataTypeList

:   list of TheHive dataType supported by the analyzer

baseConfig

:   name used for the global configuration in Cortex
    (`/etc/cortex/application.conf`)

config

:   configuration dedicated to the service of the analyzer. Typically,
    this is where we specify the TLP level of observables allowed to be
    analyzed with `check_tlp` and `max_tlp`. For example, if `max_tlp`
    is set to `3`, TLP:RED observables is allowed to be analyzed ; in
    that case, the hash of a TLP:RED file can be queried on VirusTotal.

max\_tlp

:   TLP level from which the analyzer should not be run.

  TLP      max\_tlp
  -------- ----------
  Uknown   -1
  WHITE    0
  GREEN    1
  AMBER    2
  RED      3

check\_tlp

:   if `true`, `max_tlp` is checked, else, not. Somehow, we recommand
    setting both `check_tlp` and `max_tlp` even if it is set to `false`.

command

:   command used to run the analyzer, typically path to the main
    program file.

3.  Configuration in `/etc/cortex/application.conf`

``` {.sourceCode .}
analyzer {
    config {
            global {
                    proxy {
                           http="http://myproxy:8080",
                           https="https://myproxy:8080"
                    }
            [..]
            VirusTotal {
                key="1234567890abcdef"
            }
    [..]
```

That way, you can have an analyzer with many services with different
configuration parameters.

### Output

The output of an analyzer varies if it succeeds or not.

-   If the analyzer **fails** :

    ``` {.sourceCode .json}
    {
        "success": false,
        "errorMessage":".."
    }
    ```

    -   `success` sets to `false` indicates that something went wrong
        during the execution,
    -   `errorMessage` is free text - typically the error ouput message.
-   If it **succeeds** - the analyzer run without error :

    ``` {.sourceCode .json}
    {
        "success":true
        "artifacts":[..],
        "summary":{
            "taxonomies":[..]
        },
        "full":{..}
    }
    ```

    -   `success` set to `true` indicates that the analyzer has run
        successfully,
    -   `artifacts` is a list of indicators extracted from the report
    -   `full` is the full report of the analyzer. It is free
        JSON formatted.
    -   `summary` is used in TheHive for short reports displayed in the
        list and in the detailed page of Observables. Since June'17, it
        is formatted to be a list of taxonomies.
        -   `taxonomies`:

        ``` {.sourceCode .json}
        "taxonomies":[
          {
              "namespace": "NAME",
              "predicate": "PREDICATE",
              "value": "\"VALUE\"",
              "level":"info"            <== info/safe/suspicious/malicious
          }
        ]
        ```

        -   `namespace` and `predicate` are free value. For example *VT*
            and *Score*.
        -   `level` intend to tell the maliciousness of the information
            :
            -   `info` : the analyzer produced an information, and the
                short report is shown in blue color in TheHive
            -   `safe` : the analyzer did not find anything suspicious
                or the analyzed observable is safe according to
                the analyzer. TheHive displays the short report in green
                color
            -   `suspicious` : the analyzer found that the observable is
                either suspicious or warrants further investigation. The
                short report is orange colored in TheHive
            -   `malicious` : the analyzer found that the observable
                is malicious. The short report is red colored in TheHive
        -   For more information about `summary()` and `taxonomy`, [a
            blog
            post](https://blog.thehive-project.org/2017/07/05/all-fresh-cortexutils-new-cortex-analyzers/)
            has been published in july'17 and details everything
            with examples.

Cortexutils python library
--------------------------

Until now all analyzers have been written in python and cortexutils
library has been released to help people to easily wite programs. But
python is not mandatory and any language that runs on Linux systems is
allowed.

Cortexutils is a set of classes that aims to make users write Cortex
analyzers easier. It is available for versions 2 and 3 of python.

To install it :

``` {.sourceCode .bash}
pip install cortexutils
```

or

``` {.sourceCode .bash}
pip3 install cortexutils
```

This library is already used by all analyzers released in our [github
repository](https://github.com/CERT-BDF/Cortex-Analyzers). Feel free to
start reading some of them to help you writing your own.

Display into TheHive using templates
------------------------------------

TheHive submits an observable to Cortex for analysis. Once finished,
Cortex returns the result to TheHive. This result is loaded into TheHive
with HTML templates.

### Input result

TheHive input result is simply the JSON formatted analyzer's result
transmitted by Cortex.

-   `summary` is read to display short reports in observables list and
    detailed observable page. In TheHive application, it is stored in a
    **dict** object named `content`.
-   `full` is read to display long reports when clicking the short
    report in observabled list, or in a detailed observable page. In
    TheHive application, it is stored in a **dict** object named
    `content`.

### Output

In the event that analyzers templates are not imported into TheHive :

-   in observables list, TheHive is able to display analyzers `summary`
    results using a builtin style sheet associated with the previously
    described taxonomy.
-   in observables detailed pages, `full` results can be displayed in
    raw format.
-   in observables detailed pages, `summary` results is not displayed.

If templates are imported into TheHive:

-   Short reports are displayed in Observables list and Observable
    detailed page

![VT short report](img/sc-short-vt.png){width="100px"}

-   Long reports are displayed when clicking on short reports or in
    Observable detailed page.

![VT long report](img/sc-long-vt.jpg){width="600px"}

### Writing templates

To display results into TheHive it is possible to write html templates
and feed TheHive with ; one file for long report and another for short
report.

If the analyzer is made for different services, there should be
templates for each of these. For example, for the VirusTotal analyzer:

``` {.sourceCode .bash}
thehive-templates/VirusTotal_GetReport_3_0
├── long.html
└── short.html
thehive-templates/VirusTotal_Scan_3_0
├── long.html
└── short.html
```

The folder's name is built with the `name` and the `version` found on
JSON files analyzers definition.

TheHive uses Bootstrap and AngularJS so they can be used in templates.

#### Short report template (short.html)

Until July'17, `short report` has been normalized to use taxonomies and
is built into the analyzers by the `summary()` function. We then updated
all short report templates to be able to read it :

``` {.sourceCode .html}
<span class="label" ng-repeat="t in content.taxonomies"
  ng-class="{'info': 'label-info', 'safe': 'label-success',
  'suspicious': 'label-warning',
  'malicious':'label-danger'}[t.level]">
    {{t.namespace}}:{{t.predicate}}={{t.value}}
</span>
```

If you want to change of add information display in short report in
observables detailed information page, you have to update the
`summary()` function in the analyzer's program and edit this file as
well. Basically, copy the code in your *short.html* template and it will
do the job.

#### Long report template (long.html)

Long report template is free to write as long as it is valid JSON
formatted. Basically, feel free to check what has already been written
for existing analyzers to help you start writing it.

A good start can be :

``` {.sourceCode .html}
<!-- Success -->
<div class="panel panel-danger" ng-if="success">
    <div class="panel-heading">
        ANALYZERNAME Report
    </div>
    <div class="panel-body">
        [...]                      <= code here
    </div>
</div>

<!-- General error  -->
<div class="panel panel-danger" ng-if="!success">
    <div class="panel-heading">
        <strong>{{(artifact.data || artifact.attachment.name) | fang}}</strong>
    </div>
    <div class="panel-body">
        <dl class="dl-horizontal" ng-if="content.errorMessage">
            <dt><i class="fa fa-warning"></i> ANALYZERNAME: </dt>
            <dd class="wrap">{{content.errorMessage}}</dd>
        </dl>
    </div>
</div>
```

Submit an analyzer
------------------

If you want to share your analyzer with the community through our Github
repository, we invite you to follow this guide.

Our reviews ensure that there is coherence between each analyzer, in the
way they are configured, run or displayed in TheHive. Here are some tips
you can check before submitting an analyzer.

### First rule

Consider creating one Pull Request per analyzer. We have to test and
review your analyzers, thus, this way provides the chance to release
them as quick as possible.

### Check your .json Configuration file(s)

By looking at VirusTotal analyzer, let's check JSON analyzer's
configuration file(s) :

``` {.sourceCode .json}
{
    "name": "VirusTotal_GetReport",
    "version": "3.0",
    "author": "CERT-BDF",
    "url": "https://github.com/CERT-BDF/Cortex-Analyzers",
    "license": "AGPL-V3",
    "description": "Get the latest VirusTotal report for a file, hash, domain or an IP address",
    "dataTypeList": ["file", "hash", "domain", "ip"],
    "baseConfig": "VirusTotal",
    "config": {
        "check_tlp": true,
        "max_tlp": 3,
        "service": "get"
    },
    "command": "VirusTotal/virustotal.py"
}
```

Ensure that all information is right and particularly `author` and
`license` are what you expect.

### Requirements file

If written in python language, be sure to complete the
`requirements.txt` file with the list of external library needed to run
the analyzer correctly.

### summary()

We chose to use a formatted summary report to match a taxonomy as
described above. This, to display short reports in the Observables list
in TheHive. If you want your analyzer reports in the Observable lists,
ensure that your summary matches this format. If your analyzer is
written in Python language and you are using our cortexutils library,
you can use summary() and `build_taxonomy()` functions.

### Global configuration in Cortex

When submitting your analyzer, please provide the necessary global
configuration in `/etc/cortex/application.conf` if needed.

### Simple checks

-   \[ \] Works with waited configuration, TLP or dataType
-   \[ \] Works with missing configuration, dataType or TLP : report
    explicit error message
-   \[ \] long report template manage error messages

