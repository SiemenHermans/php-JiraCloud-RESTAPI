# PHP JIRA Cloud REST-API

[![StyleCI](https://styleci.io/repos/524112147/shield?branch=main&style=flat)](https://styleci.io/repos/524112147)
[![Latest Stable Version](http://poser.pugx.org/lesstif/jira-cloud-restapi/v)](https://packagist.org/packages/lesstif/jira-cloud-restapi)
[![Latest Unstable Version](http://poser.pugx.org/lesstif/jira-cloud-restapi/v/unstable)](https://packagist.org/packages/lesstif/jira-cloud-restapi)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/lesstif/php-JiraCloud-RESTAPI/badges/quality-score.png?b=main)](https://scrutinizer-ci.com/g/lesstif/php-JiraCloud-RESTAPI/badges/quality-score.png?b=main)
[![Total Downloads](http://poser.pugx.org/lesstif/jira-cloud-restapi/downloads)](https://packagist.org/packages/lesstif/jira-cloud-restapi)
[![Monthly Downloads](http://poser.pugx.org/lesstif/jira-cloud-restapi/d/monthly)](https://packagist.org/packages/lesstif/jira-cloud-restapi)
[![Daily Downloads](http://poser.pugx.org/lesstif/jira-cloud-restapi/d/daily)](https://packagist.org/packages/lesstif/jira-cloud-restapi) 
[![License](http://poser.pugx.org/lesstif/jira-cloud-restapi/license)](https://packagist.org/packages/lesstif/jira-cloud-restapi) 
[![PHP Version Require](http://poser.pugx.org/lesstif/jira-cloud-restapi/require/php)](https://packagist.org/packages/lesstif/jira-cloud-restapi)

# Cloud only
If you want to interact with Jira On-premise(Server or Data Center) instead of Cloud, [use this repository](https://github.com/lesstif/php-jira-rest-client).

# Requirements

- PHP >= 8.1
- [php JsonMapper](https://github.com/netresearch/jsonmapper)
- [phpdotenv](https://github.com/vlucas/phpdotenv)
- [adf-tools](https://github.com/DamienHarper/adf-tools)

# Installation

1. Download and Install PHP Composer.

   ``` sh
   curl -sS https://getcomposer.org/installer | php
   ```

2. Next, run the Composer command to install the latest version of php jira rest client.
   ``` sh
   php composer.phar require lesstif/jira-cloud-restapi:^1.0
   ```
    or add the following to your composer.json file.
   ```json
   {
       "require": {
           "lesstif/jira-cloud-restapi": "^1.0"
       }
   }
   ```

3. Then run Composer's install or update commands to complete installation. 

   ```sh
   php composer.phar install
   ```

4. After installing, you need to require Composer's autoloader:

   ```php
   require 'vendor/autoload.php';
   ```

**Laravel:** Once installed, if you are not using automatic package discovery, then you need to register the `JiraCloud\JiraCloudServiceProvider` service provider in your `config/app.php`.

# Configuration

you can choose loads environment variables either 'dotenv' or 'array'.

## use dotenv


copy .env.example file to .env on your project root.	

```sh
JIRAAPI_V3_HOST='https://your-jira.atlassian.net'
JIRAAPI_V3_USER='jira-username'
JIRAAPI_V3_PERSONAL_ACCESS_TOKEN='your-access-token-here'
## to enable session cookie authorization
# JIRAAPI_V3_COOKIE_AUTH_ENABLED=true
# JIRAAPI_V3_COOKIE_FILE=storage/jira-cookie.txt
## if you are behind a proxy, add proxy settings
JIRAAPI_V3_PROXY_SERVER='your-proxy-server'
JIRAAPI_V3_PROXY_PORT='proxy-port'
JIRAAPI_V3_PROXY_USER='proxy-username'
JIRAAPI_V3_PROXY_PASSWORD='proxy-password'
```
**CAUTION**
this library not fully supported JIRA REST API V3 yet. 

## use array

create Service class with ArrayConfiguration parameter.

```php
use JiraCloud\Configuration\ArrayConfiguration;
use JiraCloud\Issue\IssueService;

$iss = new IssueService(new ArrayConfiguration(
          [
               'jiraHost' => 'https://your-jira.atlassian.net',                                           
               'jiraUser' => 'jira-username',              
               'personalAccessToken' => 'your-token-here',
                
                // custom log config
               'jiraLogEnabled' => true,
               'jiraLogFile' => "my-jira-rest-client.log",
               'jiraLogLevel' => 'INFO',
        
               // to enable session cookie authorization (with basic authorization only)
               'cookieAuthEnabled' => true,
               'cookieFile' => storage_path('jira-cookie.txt'),
               // if you are behind a proxy, add proxy settings
               'proxyServer' => 'your-proxy-server',
               'proxyPort' => 'proxy-port',
               'proxyUser' => 'proxy-username',
               'proxyPassword' => 'proxy-password',
          ]
   ));
```

# Usage

## Table of Contents

### Project
- [Create Project](#create-project)
- [Update Project](#update-project)
- [Delete Project](#delete-project)
- [Get Project Info](#get-project-info)
- [Get All Project list](#get-all-project-list)
- [Get Project Components](#get-project-components)
- [Get Project Type](#get-project-type)
- [Get Project Version](#get-project-version)

### Custom Field
- [Get All Field list](#get-all-field-list)
- [Create Custom Field](#create-custom-field)

### Issue
- [Get Issue Info](#get-issue-info)
- [Create Issue](#create-issue)
- [Create Issue - bulk](#create-multiple-issues)
- [Create Sub Task](#create-sub-task)
- [Create Issue using REST API V3](#create-issue-using-rest-api-v3)
- [Add Attachment](#add-attachment)
- [Update issue](#update-issue)
    - [Update Labels](#update-labels)
    - [Update Fix Versions](#update-fix-versions)
- [Change assignee](#change-assignee)
- [Remove issue](#remove-issue)
- [Perform a transition on an issue](#perform-a-transition-on-an-issue)
- [Perform an advanced search, using the JQL](#perform-an-advanced-search)
    - [Simple JQL](#simple-query)
    - [Simple Query with LinkedIssue](#simple-query-with-linkedissue)
    - [JQL With pagination](#jql-with-pagination)
    - [Using JQL Query class](#jql-query-class)
- [Remote Issue Link](#remote-issue-link)
    - [Get Remote Issue Link](#get-remote-issue-link)
    - [Create Remote Issue Link](#create-remote-issue-link)
- [Issue time tracking](#issue-time-tracking)
- [Add worklog in Issue](#add-worklog-in-issue)
- [Edit worklog in Issue](#edit-worklog-in-issue)
- [Get Issue worklog](#get-issue-worklog)
- [Add watcher to Issue](#add-watcher-to-issue)
- [Remove watcher from Issue](#remove-watcher-from-issue)
- [Send a notification to the recipients](#issue-notify)

### Comment
- [Add comment](#add-comment)
- [Get comment](#get-comment)
- [Delete comment](#delete-comment)
- [Update comment](#update-comment)

### IssueLink

* [Create Issue Link](#create-issue-link)
* [Get Issue LinkType](#get-issue-linktype)

### User
- [Create User](#create-user)
- [Get User Info](#get-user-info)
- [Find Users](#find-users)
- [Find Assignable Users](#find-assignable-users)
- [Find Users by query](#find-users-by-query)
- [Delete User](#delete-user)
- [Update User](#update-user)

### Group
- [Create Group](#create-group)
- [Get Users from group](#get-users-from-group)
- [Add User to group](#add-user-to-group)
- [Remove User from group](#remove-user-from-group)

### Priority
- [Get All Priority list](#get-all-priority-list)
- [Get Priority](#get-priority)

### Attachment
- [Get attachment Info](#get-attachment-info)
- [Remove attachment](#remove-attachment)

### Version
- [Create version](#create-version)
- [Update version](#update-version)
- [Delete version](#delete-version)
- [Get version related issues](#get-version-related-issues)
- [Get version unresolved issues](#get-version-related-issues)

### Component
- [Create component](#create-component)
- [Update component](#update-component)
- [Delete component](#delete-component)

### Board
- [Get board list](#get-board-list)
- [Get board info](#get-board-info)
- [Get board issues](#get-board-issues)
- [Get board epics](#get-board-epics)

### Epic
- [Get epic info](#)

#### Create Project

Create a new project.

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-projects/#api-rest-api-3-project-post)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\Project\Project;
use JiraCloud\JiraException;

try {
    $p = new Project();

    $p->setKey('EX')
        ->setName('Example')
        ->setProjectTypeKey('business')
        ->setProjectTemplateKey('com.atlassian.jira-core-project-templates:jira-core-project-management')
        ->setDescription('Example Project description')
        ->setLeadName('lesstif')
        ->setUrl('http://example.com')
        ->setAssigneeType('PROJECT_LEAD')
        ->setAvatarId(10130)
        ->setIssueSecurityScheme(10000)
        ->setPermissionScheme(10100)
        ->setNotificationScheme(10100)
        ->setCategoryId(10100)
    ;

    $proj = new ProjectService();

    $pj = $proj->createProject($p);
   
    // 'http://example.com/rest/api/2/project/10042'
    var_dump($pj->self);
    // 10042 
    var_dump($pj->id);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

#### Update Project

Update a project.
Only none null values sent in JSON will be updated in the project.

Values available for the assigneeType field are: 'PROJECT_LEAD' and 'UNASSIGNED'.

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-projects/#api-rest-api-3-project-projectidorkey-put)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\Project\Project;
use JiraCloud\JiraException;

try {
    $p = new Project();

    $p->setName('Updated Example')
        ->setProjectTypeKey('software')
        ->setProjectTemplateKey('com.atlassian.jira-software-project-templates:jira-software-project-management')
        ->setDescription('Updated Example Project description')
        ->setLead('new-leader')
        ->setUrl('http://new.example.com')
        ->setAssigneeType('UNASSIGNED')
    ;

    $proj = new ProjectService();

    $pj = $proj->updateProject($p, 'EX');
   
    var_dump($pj);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

#### Delete Project

Deletes a project.

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-projects/#api-rest-api-3-project-projectidorkey-delete)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $proj = new ProjectService();

    $pj = $proj->deleteProject('EX');
   
    var_dump($pj);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

#### Get Project Info

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-projects/#api-rest-api-3-project-projectidorkey-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $proj = new ProjectService();

    $p = $proj->get('TEST');
	
    var_dump($p);			
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}
```

#### Get All Project list

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-projects/#api-rest-api-3-project-search-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $proj = new ProjectService();

    $prjs = $proj->getAllProjects();

    foreach ($prjs as $p) {
        echo sprintf('Project Key:%s, Id:%s, Name:%s, projectCategory: %s\n',
            $p->key, $p->id, $p->name, $p->projectCategory['name']
        );			
    }			
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}

```

#### Get Project Components

[See Jira API reference (Get project components)](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-project-components/#api-rest-api-3-component-id-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $proj = new ProjectService();

    $prjs = $proj->getAllProjects();

    // Extract and show Project Components for every Jira Project
    foreach ($prjs as $p) {
        var_export($proj->getProjectComponents($p->id));
    }
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get Project type

[See Jira API reference (get all types)](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-project-types/#api-rest-api-3-project-type-get)

[See Jira API reference (get type)](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-project-types/#api-rest-api-3-project-type-projecttypekey-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $proj = new ProjectService();

    // get all project type
    $prjtyps = $proj->getProjectTypes();

    foreach ($prjtyps as $pt) {
        var_dump($pt);
    }

    // get specific project type.
    $pt = $proj->getProjectType('software');
    var_dump($pt);

} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}

```

#### Get Project Version

get all project's versions.

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-project-versions/#api-rest-api-3-project-projectidorkey-versions-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $proj = new ProjectService();

    $vers = $proj->getVersions('TEST');

    foreach ($vers as $v) {
        // $v is  JiraCloud\Issue\Version
        var_dump($v);
    }
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}

```

or get paginated project's versions.

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-project-versions/#api-rest-api-3-project-projectidorkey-version-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $param = [
        'startAt' => 0,
        'maxResults' => 10,
        'orderBy' => 'name',
        //'expand' => null,
    ];

    $proj = new ProjectService();

    $vers = $proj->getVersionsPagenated('TEST', $param);

    foreach ($vers as $v) {
        // $v is  JiraCloud\Issue\Version
        var_dump($v);
    }
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}

```


#### Get All Field List

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-fields/#api-rest-api-3-field-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Field\Field;
use JiraCloud\Field\FieldService;
use JiraCloud\JiraException;

try {
    $fieldService = new FieldService();

    // return custom field only. 
    $ret = $fieldService->getAllFields(Field::CUSTOM); 
    	
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'testSearch Failed : '.$e->getMessage());
}
```

#### Create Custom Field

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/field-createCustomField)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Field\Field;
use JiraCloud\Field\FieldService;
use JiraCloud\JiraException;

try {
    $field = new Field();
    
    $field->setName('New custom field')
            ->setDescription('Custom field for picking groups')
            ->setType('com.atlassian.jira.plugin.system.customfieldtypes:grouppicker')
            ->setSearcherKey('com.atlassian.jira.plugin.system.customfieldtypes:grouppickersearcher');

    $fieldService = new FieldService();

    $ret = $fieldService->create($field);
    
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'Field Create Failed : '.$e->getMessage());
}
```

If you need a list of custom field types(ex. *com.atlassian.jira.plugin.system.customfieldtypes:grouppicker*) , check out [Get All Field list](#get-all-field-list).

#### Get Issue Info

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-getIssue)

Returns a full representation of the issue for the given issue key.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

try {
    $issueService = new IssueService();
	
    $queryParam = [
        'fields' => [  // default: '*all'
            'summary',
            'comment',
        ],
        'expand' => [
            'renderedFields',
            'names',
            'schema',
            'transitions',
            'operations',
            'editmeta',
            'changelog',
        ]
    ];
            
    $issue = $issueService->get('TEST-867', $queryParam);
	
    var_dump($issue->fields);	
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}
```

You can access the custom field associated with issue through *$issue->fields->customFields* array or through direct custom field id variables(Ex: *$issue->fields->customfield_10300*).

#### Create Issue

All Jira v3 API users must use the [Atlassian Document Format (ADF)](https://developer.atlassian.com/cloud/jira/platform/apis/document/structure/) for comment and description fields.
It's represents rich text stored in Atlassian products, so very complicated.

For that reason, I used the [amazing adf-tools](https://github.com/DamienHarper/adf-tools) create by [DamienHarper](https://github.com/DamienHarper).

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-post)
```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\IssueField;
use JiraCloud\JiraException;
use DH\Adf\Node\Block\Document;
use JiraCloud\ADF\AtlassianDocumentFormat;

try {
    $issueField = new IssueField();
    
    $code =<<<CODE
<?php
\$i = 123;
\$a = ['hello', 'world', ];
var_dump([\$i => \$a]);
CODE;

    $doc = (new Document())
        ->heading(1)            // header level 1, can have child blocks (needs to be closed with `->end()`)
          ->text('h1')        // simple unstyled text, cannot have child blocks (no `->end()` needed)
        ->end()                 // closes `heading` node
        ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
            ->text('we’re ')    // simple unstyled text
            ->strong('support') // text node embedding a `strong` mark
            ->text(' ')         // simple unstyled text
            ->em('markdown')    // text node embedding a `em` mark
            ->text('. ')        // simple unstyled text
            ->underline('like') // text node embedding a `underline` mark
            ->text(' this.')    // simple unstyled text
        ->end()                 // closes `paragraph` node
        ->heading(2)            // header level 2
            ->text('h2')        // simple unstyled text
        ->end()                 // closes `heading` node
        ->heading(3)
            ->text('heading 3')
        ->end()
        ->paragraph()           // paragraph
            ->text('also support heading.') // simple unstyled text
        ->end()                 // closes `paragraph` node
        ->codeblock('php')
           ->text($code)
        ->end()
    ;

    $descV3 = new AtlassianDocumentFormat($doc);         

    $issueField->setProjectKey('TEST')
                ->setSummary('something\'s wrong')
                ->setAssigneeNameAsString('lesstif')
                ->setPriorityNameAsString('Highest')
                ->setIssueTypeAsString('Story')
                ->setDescription($descV3)
                ->addVersionAsString('1.0.1')
                ->addVersionAsArray(['1.0.2', '1.0.3'])
                ->addComponentsAsArray(['Component-1', 'Component-2'])
                // set issue security if you need.
                ->setSecurityId(10001 /* security scheme id */)
                ->setDueDateAsString('2023-06-19')
                // or you can use DateTimeInterface
                //->setDueDateAsDateTime(
                //            (new DateTime('NOW'))->add(DateInterval::createFromDateString('1 month 5 day'))
                // )
            ;
	
    $issueService = new IssueService();

    $ret = $issueService->create($issueField);
	
    //If success, Returns a link to the created issue.
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}
```

If you want to set custom field, you can call the *addCustomField* function with custom field id and value as parameters.

```php
try {
    $issueField = new IssueField();

    $doc = (new Document())        
        ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
            ->text('Full description for issue  ')    // simple unstyled text            
        ->end()                 // closes `paragraph` node
        
    $descV3 = new AtlassianDocumentFormat($doc);
    
    $issueField->setProjectKey('TEST')
                ->setSummary('something\'s wrong')
                ->setAssigneeNameAsString('lesstif')
                ->setPriorityNameAsString('Critical')
                ->setIssueTypeAsString('Bug')
                ->setDescription($descV3)
                ->addVersionAsString('1.0.1')
                ->addVersionAsString('1.0.3')
                ->addCustomField('customfield_10100', 'text area body text') // String type custom field
                ->addCustomField('customfield_10200', ['value' => 'Linux']) // Select List (single choice)
                ->addCustomField('customfield_10408', [
                    ['value' => 'opt2'], ['value' => 'opt4']
                ]) // Select List (multiple choice)
    ;
	
    $issueService = new IssueService();

    $ret = $issueService->create($issueField);
	
    //If success, Returns a link to the created issue.
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

Currently, not tested for all custom field types.

#### Create Multiple Issues

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-createIssues)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\IssueField;
use JiraCloud\JiraException;
use JiraCloud\ADF\ADFMarkType;
use JiraCloud\ADF\AtlassianDocumentFormat;

try {
    $issueFieldOne = new IssueField();

    $doc = (new Document())        
        ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
            ->text('Full description for issue  ')    // simple unstyled text            
        ->end()                 // closes `paragraph` node
    $descV3 = new AtlassianDocumentFormat($doc);
    
    $issueFieldOne->setProjectKey('TEST')
                ->setSummary('something\'s wrong')
                ->setPriorityNameAsString('Critical')
                ->setIssueTypeAsString('Bug')
                ->setDescription($descV3);

    $issueFieldTwo = new IssueField();

    $doc2 = (new Document())        
        ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
            ->text('Full description for second issue  ')    // simple unstyled text            
        ->end()                 // closes `paragraph` node
	
    $desc2 = new AtlassianDocumentFormat(doc2);
    
    $issueFieldTwo->setProjectKey('TEST')
                ->setSummary('something else is wrong')
                ->setPriorityNameAsString('Critical')
                ->setIssueTypeAsString('Bug')
                ->setDescription($desc2);
    
    $issueService = new IssueService();

    $ret = $issueService->createMultiple([$issueFieldOne, $issueFieldTwo]);
    
    //If success, returns an array of the created issues
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

#### Create Sub Task

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-createIssue)

Creating a sub-task is similar to creating a regular issue, with two important method calls:

```php
->setIssueTypeAsString('Sub-task')
->setParentKeyOrId($issueKeyOrId)
```

for example
                
```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\IssueField;
use JiraCloud\JiraException;
use JiraCloud\ADF\ADFMarkType;
use JiraCloud\ADF\AtlassianDocumentFormat;

try {
    $issueField = new IssueField();

    $doc = (new Document())        
        ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
            ->text('Full description for sub-task issue  ')    // simple unstyled text            
        ->end()                 // closes `paragraph` node
    $descV3 = new AtlassianDocumentFormat(doc);
    
    $issueField->setProjectKey('TEST')
                ->setSummary('something\'s wrong')
                ->setAssigneeNameAsString('lesstif')
                ->setPriorityNameAsString('Critical')
                ->setDescription($descV3)
                ->addVersionAsString('1.0.1')
                ->addVersionAsString('1.0.3')
                ->setIssueTypeAsString('Sub-task')  //issue type must be Sub-task
                ->setParentKeyOrId('TEST-143')  //Issue Key
    ;

    $issueService = new IssueService();

    $ret = $issueService->create($issueField);

    //If success, Returns a link to the created sub task.
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

#### Create Issue using REST API V3

REST API V3' description field is complicated.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\IssueFieldV3;
use JiraCloud\Issue\DescriptionV3;
use JiraCloud\JiraException;

try {
    $issueField = new IssueFieldV3();

    $paraDesc =<<< DESC
Full description for issue
- order list 1
- order list 2
-- sub order list 1
-- sub order list 1
- order list 3 
DESC;
            $descV3 = new DescriptionV3();
            $descV3->addDescriptionContent('paragraph', $paraDesc);

            $issueField->setProjectKey('TEST')
                ->setSummary("something's wrong")
                ->setAssigneeAccountId('user-account-id-here')
                ->setPriorityNameAsString('Critical')
                ->setIssueTypeAsString('Bug')
                ->setDescriptionV3($descV3)
            ;
	
    $issueService = new IssueService();

    $ret = $issueService->create($issueField);
	
    //If success, Returns a link to the created issue.
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}
```

If you want to set custom field, you can call the *addCustomField* function with custom field id and value as parameters.

```php
try {
    $issueField = new IssueField();

    $issueField->setProjectKey('TEST')
                ->setSummary('something\'s wrong')
                ->setAssigneeNameAsString('lesstif')
                ->setPriorityNameAsString('Critical')
                ->setIssueTypeAsString('Bug')
                ->setDescription('Full description for issue')
                ->addVersionAsString('1.0.1')
                ->addVersionAsString('1.0.3')
                ->addCustomField('customfield_10100', 'text area body text') // String type custom field
                ->addCustomField('customfield_10200', ['value' => 'Linux']) // Select List (single choice)
                ->addCustomField('customfield_10408', [
                    ['value' => 'opt2'], ['value' => 'opt4']
                ]) // Select List (multiple choice)
    ;
	
    $issueService = new IssueService();

    $ret = $issueService->create($issueField);
	
    //If success, Returns a link to the created issue.
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

Currently, not tested for all custom field types.

#### Add Attachment

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue/%7BissueIdOrKey%7D/attachments-addAttachment)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-879';

try {
    $issueService = new IssueService();

    // multiple file upload support.
    $ret = $issueService->addAttachments($issueKey, 
        ['screen_capture.png', 'bug-description.pdf', 'README.md']
    );

    print_r($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(FALSE, 'Attach Failed : ' . $e->getMessage());
}

```

#### Update issue

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-editIssue)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\IssueField;
use JiraCloud\JiraException;
use JiraCloud\ADF\ADFMarkType;
use JiraCloud\ADF\AtlassianDocumentFormat;

$issueKey = 'TEST-879';

try {			
    $issueField = new IssueField(true);

    $doc = (new Document())        
        ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
            ->text('This is a shorthand for a set operation on the summary field ')    // simple unstyled text            
        ->end()                 // closes `paragraph` node
     ;
    
    $descV3 = new AtlassianDocumentFormat(doc);
            
    $issueField->setAssigneeNameAsString('admin')
                ->setPriorityNameAsString('Blocker')
                ->setIssueTypeAsString('Task')
                ->addLabel('test-label-first')
                ->addLabel('test-label-second')
                ->addVersionAsString('1.0.1')
                ->addVersionAsString('1.0.2')
                ->setDescription($descV3)
    ;

    // optionally set some query params
    $editParams = [
        'notifyUsers' => false,
    ];

    $issueService = new IssueService();

    // You can set the $paramArray param to disable notifications in example
    $ret = $issueService->update($issueKey, $issueField, $editParams);

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(FALSE, 'update Failed : ' . $e->getMessage());
}
```

If you want to change the custom field type when updating an issue, you can call the *addCustomField* function just as you did for creating issue.


##### Update labels

This function is a convenient wrapper for add or remove label in the issue.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

try {
    $issueKey = 'TEST-123';

    $issueService = new IssueService();

    $addLabels = [
        'triaged', 'customer-request', 'sales-request'
    ];

    $removeLabel = [
        'will-be-remove', 'this-label-is-typo'
    ];

    $ret = $issueService->updateLabels($issueKey,
            $addLabels,
            $removeLabel,
            $notifyUsers = false
        );

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'updateLabels Failed : '.$e->getMessage());
}
```

##### Update fix versions

This function is a convenient wrapper for add or remove fix version in the issue.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

try {
    $issueKey = 'TEST-123';

    $issueService = new IssueService();

    $addVersions = [
        '1.1.1', 'named-version'
    ];

    $removeVersions = [
        '1.1.0', 'old-version'
    ];

    $ret = $issueService->updateFixVersions($issueKey,
            $addVersions,
            $removeVersions,
            $notifyUsers = false
        );

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'updateFixVersions Failed : '.$e->getMessage());
}
```

#### Change Assignee

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-assignee-put)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-879';

try {
	$issueService = new IssueService();

    // if assignee is -1, automatic assignee used.
    // A null assignee will remove the assignee.
    $accountId = 'replace-to-user-account-id';

    $ret = $issueService->changeAssigneeByAccountId($issueKey, $accountId);

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(FALSE, 'Change Assignee Failed : ' . $e->getMessage());
}
```

REST API V3(JIRA Cloud) users must use *changeAssigneeByAccountId* method with accountId.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-879';

try {
	$issueService = new IssueService();

    $accountId = 'usre-account-id';

    $ret = $issueService->changeAssigneeByAccountId($issueKey, $accountId);

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(FALSE, 'Change Assignee Failed : ' . $e->getMessage());
}
```   

#### Remove Issue

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-deleteIssue)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-879';

try {
    $issueService = new IssueService();

    $ret = $issueService->deleteIssue($issueKey);
    // if you want to delete issues with sub-tasks
    //$ret = $issueService->deleteIssue($issueKey, array('deleteSubtasks' => 'true'));

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(FALSE, 'Remove Issue Failed : ' . $e->getMessage());
}
```

#### Add comment

Not working at this time.!

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-comments/#api-rest-api-3-issue-issueidorkey-comment-post)

```php
<?php
require 'vendor/autoload.php';

use DH\Adf\Node\Block\Document;
use JiraCloud\ADF\AtlassianDocumentFormat;
use JiraCloud\Issue\Comment;
use JiraCloud\Issue\IssueService;

$issueKey = 'TEST-879';

try {			
    $comment = new Comment();

    $code =<<<CODE
<?php
\$i = 123;
\$a = ['hello', 'world', ];
var_dump([\$i => \$a]);
CODE;

    $doc = (new Document())
        ->heading(1)            // header level 1, can have child blocks (needs to be closed with `->end()`)
            ->text('h1')        // simple unstyled text, cannot have child blocks (no `->end()` needed)
        ->end()                 // closes `heading` node
        ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
            ->text('we’re ')    // simple unstyled text
            ->strong('support') // text node embedding a `strong` mark
            ->text(' ')         // simple unstyled text
            ->em('markdown')    // text node embedding a `em` mark
            ->text('. ')        // simple unstyled text
            ->underline('like') // text node embedding a `underline` mark
            ->text(' this.')    // simple unstyled text
            ->text(' date=' . date("Y-m-d H:i:s"))
        ->end()                 // closes `paragraph` node
        ->heading(2)            // header level 2
          ->text('h2')        // simple unstyled text
        ->end()                 // closes `heading` node
        ->heading(3)
            ->text('heading 3')
        ->end()
        ->paragraph()           // paragraph
          ->text('also support heading.') // simple unstyled text
        ->end()                 // closes `paragraph` node
        ->codeblock('php')
         ->text($code)
        ->end()
    ;

    $comment->setBodyByAtlassianDocumentFormat($doc);

    $issueService = new IssueService();
    $ret = $issueService->addComment($subTaskIssueKey, $comment);
            
    print_r($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(FALSE, 'add Comment Failed : ' . $e->getMessage());
}

```

#### Get comment

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-comments/#api-rest-api-3-issue-issueidorkey-comment-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-879';

try {
    $issueService = new IssueService();
    
    $param = [
         'startAt' => 0, 
         'maxResults' => 3,
         'expand' => 'renderedBody',
    ];
   
    $comments = $issueService->getComments($issueKey, $param);

    // $comments->comments is a real array of comment
    foreach ($comments->comments as $comment){
        var_dump(["id" => comment->id, "self" => $comment->self]);
    }  
    
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'get Comment Failed : '.$e->getMessage());
}
```

get comment by comment id

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-879';

try {
    $issueService = new IssueService();
    
    $param = [
         'startAt' => 0, 
         'maxResults' => 3,
         'expand' => 'renderedBody',
    ];
    $commentId = 13805;

    $comments = $issueService->getComment($issueKey, $commentId, $param);

    var_dump($comments);

} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'get Comment Failed : '.$e->getMessage());
}
```

#### Delete comment

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-comments/#api-rest-api-3-issue-issueidorkey-comment-id-delete)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-879';

try {
    $commentId = 12345;

    $issueService = new IssueService();

    $ret = $issueService->deleteComment($issueKey, $commentId);

} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'Delete comment Failed : '.$e->getMessage());
}

```

#### Update comment

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-comments/#api-rest-api-3-issue-issueidorkey-comment-id-put)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;
use JiraCloud\Issue\Comment;

$issueKey = 'TEST-879';

try {
    $commentId = 12345;

    $issueService = new IssueService();
        
    $comment = new Comment();

    $code =<<<CODE
# This program adds two numbers
num1 = 1.5
num2 = 6.3

# Add two numbers
sum = num1 + num2

# Display the sum
print('The sum of {0} and {1} is {2}'.format(num1, num2, sum))
CODE;

    $doc = (new Document())
        ->heading(2)            // header level 1, can have child blocks (needs to be closed with `->end()`)
            ->text('h2')        // simple unstyled text, cannot have child blocks (no `->end()` needed)
        ->end()                 // closes `heading` node
        ->heading(3)            // header level 2
            ->text('h3')        // simple unstyled text
        ->end()                 // closes `heading` node
        ->heading(4)
            ->text('heading 4')
        ->end()
        ->paragraph()           // paragraph
            ->text('also support heading.') // simple unstyled text
        ->end()                 // closes `paragraph` node
        ->codeblock('python')
            ->text($code)
        ->end()
        ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
            ->text('we’re ')    // simple unstyled text
            ->strong('support') // text node embedding a `strong` mark
            ->text(' ')         // simple unstyled text
            ->em('markdown')    // text node embedding a `em` mark
            ->text('. ')        // simple unstyled text
            ->underline('like') // text node embedding a `underline` mark
            ->text(' this.')    // simple unstyled text
            ->text(' date=' . date("Y-m-d H:i:s"))
        ->end()                 // closes `paragraph` node
    ;

    $comment->setBodyByAtlassianDocumentFormat($doc);

    $issueService = new IssueService();
    $ret = $issueService->updateComment($issueKey, $comment_id, $comment);

} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'Update comment Failed : '.$e->getMessage());
}

```

#### Perform a transition on an issue

Note: this library uses goal **status names** instead of **transition names**.
So, if you want to change issue status to 'Some Status',
you should pass that status name to `setTransitionName`

i.e. `$transition->setTransitionName('Some Status')`

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issues/#api-rest-api-3-issue-issueidorkey-transitions-post)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\Transition;
use JiraCloud\JiraException;

$issueKey = 'TEST-879';

try {			
    $transition = new Transition();
    $transition->setTransitionName('In Progress');
    
    $doc = (new Document())
                ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
                    ->text('Issue ')    // simple unstyled text
                    ->strong(' status') // text node embedding a `strong` mark
                    ->text(' ')         // simple unstyled text
                    ->text(' changed ')    // text node embedding a `em` mark
                    ->text('. ')        // simple unstyled text
                    ->underline('by') // text node embedding a `underline` mark
                    ->em(' REST API.')    // simple unstyled text
                ->end()                 // closes `paragraph` node
            ;

    $comment = new AtlassianDocumentFormat($doc);
    
    $transition->setCommentBody($comment);

    $issueService = new IssueService();
    $issueService->transition($issueKey, $transition);
    
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(FALSE, 'add Comment Failed : ' . $e->getMessage());
}
```

Note: If you are JIRA with local language profiles, you must use *setUntranslatedName* instead of *setTransitionName*.

i.e. `$transition->setUntranslatedName('Done')`

#### Perform an advanced search

##### Simple Query

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/search-searchUsingSearchRequest)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$jql = 'project not in (TEST)  and assignee = currentUser() and status in (Resolved, closed)';

try {
    $issueService = new IssueService();

    $ret = $issueService->search($jql);
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'testSearch Failed : '.$e->getMessage());
}
```

##### Simple Query with LinkedIssue

[See Jira API reference](https://support.atlassian.com/jira-work-management/docs/advanced-search-reference-jql-functions/#Advancedsearchingfunctionsreference-linkedIssueslinkedIssues--)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;
use JiraCloud\Issue\JqlFunction;

// Searches for issues that are linked to an issue. You can restrict the search to links of a particular type. 
try {
    $linkedIssue = JqlFunction::linkedIssues('TEST-01', 'IN', 'is blocked by');

    $issueService = new IssueService();

    $ret = $issueService->search($linkedIssue->expression);

    var_dump($ret);
} catch (JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

// Searches for epics and subtasks. If the issue is not an epic, the search returns all subtasks for the issue. 
try {
    $linkedIssue = JqlFunction::linkedissue('TEST-01');

    $issueService = new IssueService();

    $ret = $issueService->search($linkedIssue->expression);

    var_dump($ret);
} catch (JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

##### JQL with pagination

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/search-search)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$jql = 'project not in (TEST)  and assignee = currentUser() and status in (Resolved, closed)';

try {
    $issueService = new IssueService();

    $pagination = -1;
  
    $startAt = 0;	//the index of the first issue to return (0-based)    
    $maxResult = 3;	// the maximum number of issues to return (defaults to 50). 
    $totalCount = -1;	// the number of issues to return
  
    // first fetch
    $ret = $issueService->search($jql, $startAt, $maxResult);
    $totalCount = $ret->total;
  	
    // do something with fetched data
    foreach ($ret->issues as $issue) {
        print (sprintf('%s %s \n', $issue->key, $issue->fields->summary));
    }
  	
    // fetch remained data
    $page = $totalCount / $maxResult;

    for ($startAt = 1; $startAt < $page; $startAt++) {
        $ret = $issueService->search($jql, $startAt * $maxResult, $maxResult);

        print ('\nPaging $startAt\n');
        print ('-------------------\n');
        foreach ($ret->issues as $issue) {
            print (sprintf('%s %s \n', $issue->key, $issue->fields->summary));
        }
    }     
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'testSearch Failed : '.$e->getMessage());
}
```

##### JQL query class

[See Jira API reference](https://confluence.atlassian.com/jiracoreserver/advanced-searching-939937709.html)

If you're not familiar JQL then you can use convenience JqlQuery class.
JqlFunction class can be used to add jql functions calls to query.
You can find the names of almost all fields, functions, keywords and operators
defined as constants in `JqlQuery` and static methods in `JqlFunciton` classes.
For more info see the Jira docs (link above).

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\JqlQuery;
use JiraCloud\JiraException;
use JiraCloud\Issue\JqlFunction;

try {
    $jql = new JqlQuery();

    $jql->setProject('TEST')
        ->setType('Bug')
        ->setStatus('In Progress')
        ->setAssignee(JqlFunction::currentUser())
        ->setCustomField('My Custom Field', 'value')
        ->addIsNotNullExpression('due');

    $issueService = new IssueService();

    $ret = $issueService->search($jql->getQuery());

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'testSearch Failed : '.$e->getMessage());
}
```

#### Remote Issue Link


##### get remote issue link

* [See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-getRemoteIssueLinks)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-316';

try {
    $issueService = new IssueService();

    $rils = $issueService->getRemoteIssueLink($issueKey);
        
    // rils is array of RemoteIssueLink classes
    var_dump($rils);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, $e->getMessage());
}

```

##### create remote issue link

* [See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-getRemoteIssueLinks)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\RemoteIssueLink;
use JiraCloud\JiraException;

$issueKey = 'TEST-316';

try {
    $issueService = new IssueService();

    $ril = new RemoteIssueLink();

    $ril->setUrl('http://www.mycompany.com/support?id=1')
        ->setTitle('Remote Link Title')
        ->setRelationship('causes')
        ->setSummary('Crazy customer support issue')
    ;

    $rils = $issueService->createOrUpdateRemoteIssueLink($issueKey, $ril);

    // rils is array of RemoteIssueLink classes
    var_dump($rils);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'Create Failed : '.$e->getMessage());
}
```

#### Issue time tracking

This methods use `get issue` and `edit issue` methods internally.

[See Jira API reference (get issue)](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-getIssue)

[See Jira API reference (edit issue)](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-editIssue)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\TimeTracking;
use JiraCloud\JiraException;

$issueKey = 'TEST-961';

try {
    $issueService = new IssueService();
    
    // get issue's time tracking info
    $ret = $issueService->getTimeTracking($this->issueKey);
    var_dump($ret);
    
    $timeTracking = new TimeTracking;

    $timeTracking->setOriginalEstimate('3w 4d 6h');
    $timeTracking->setRemainingEstimate('1w 2d 3h');
    
    // add time tracking
    $ret = $issueService->timeTracking($this->issueKey, $timeTracking);
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'testSearch Failed : '.$e->getMessage());
}

```

#### Add worklog in issue

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-worklogs/#api-rest-api-3-issue-issueidorkey-worklog-post)

```php
<?php
require 'vendor/autoload.php';

use DateInterval;
use DateTime;
use DH\Adf\Node\Block\Document;
use JiraCloud\ADF\AtlassianDocumentFormat;
use PHPUnit\Framework\TestCase;
use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\Worklog;
use JiraCloud\JiraException;

$issueKey = 'TEST-961';

try {
    $workLog = new Worklog();
    
    $doc = (new Document())
                ->heading(1)            // header level 1, can have child blocks (needs to be closed with `->end()`)
                    ->text('h1')        // simple unstyled text, cannot have child blocks (no `->end()` needed)
                ->end()                 // closes `heading` node
                ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
                    ->text('we’re ')    // simple unstyled text
                    ->strong('support') // text node embedding a `strong` mark
                    ->text(' ')         // simple unstyled text
                    ->em('markdown')    // text node embedding a `em` mark
                    ->text('. ')        // simple unstyled text
                    ->underline('like') // text node embedding a `underline` mark
                    ->text(' this.')    // simple unstyled text
                ->end()                 // closes `paragraph` node
                ->heading(2)            // header level 2
                    ->text('h2')        // simple unstyled text
                ->end()                 // closes `heading` node
                ->heading(3)
                    ->text('heading 3')
                ->end()
                ->paragraph()           // paragraph
                    ->text('also support heading.') // simple unstyled text
                ->end()                 // closes `paragraph` node
                ->codeblock('php')
                    ->text($code)
                ->end()
            ;

    $comment = new AtlassianDocumentFormat($doc);

    $startedAt = (new DateTime('NOW'))
        ->add(DateInterval::createFromDateString('-1 hour -27 minute'));

    $workLog->setComment($comment)
        ->setStarted($startedAt)
        ->setTimeSpent('1d 2h 3m');

    $issueService = new IssueService();

    $ret = $issueService->addWorklog($issueKey, $workLog);

    $workLogid = $ret->{'id'};

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'Create Failed : '.$e->getMessage());
}

```

#### edit worklog in issue

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-worklogs/#api-rest-api-3-issue-issueidorkey-worklog-id-put)

```php
<?php
require 'vendor/autoload.php';

use DateInterval;
use DateTime;
use DH\Adf\Node\Block\Document;
use JiraCloud\ADF\AtlassianDocumentFormat;
use PHPUnit\Framework\TestCase;
use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\Worklog;
use JiraCloud\JiraException;

$issueKey = 'TEST-961';
$workLogid = '12345';

try {
    $workLog = new Worklog();

    $doc = (new Document())
                ->heading(1)            // header level 1, can have child blocks (needs to be closed with `->end()`)
                ->text('h1')        // simple unstyled text, cannot have child blocks (no `->end()` needed)
                ->end()                 // closes `heading` node
                ->paragraph()           // paragraph, can have child blocks (needs to be closed with `->end()`)
                    ->text('I’did ')    // simple unstyled text
                    ->strong('edit') // text node embedding a `strong` mark
                    ->text(' ')         // simple unstyled text
                    ->em('previous')    // text node embedding a `em` mark
                    ->text(' ')        // simple unstyled text
                    ->underline('worklog') // text node embedding a `underline` mark
                    ->text(' here.')    // simple unstyled text
                ->end()                 // closes `paragraph` node
                ->heading(2)            // header level 2
                 ->text('h2')        // simple unstyled text
                ->end()                 // closes `heading` node
                ->heading(3)
                 ->text('heading 3')
                ->end()
                ->paragraph()           // paragraph
                 ->text('also support heading.') // simple unstyled text
                ->end()                 // closes `paragraph` node
            ;

    $comment = new AtlassianDocumentFormat($doc);

    $workLog->setComment($comment)
        ->setTimeSpent('2d 7h 5m');

    $issueService = new IssueService();

    $ret = $issueService->editWorklog($issueKey, $workLog, $workLogid);

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'Edit worklog Failed : '.$e->getMessage());
}

```

#### Get issue worklog

[See Jira API reference (get full issue worklog)](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-worklogs/#api-rest-api-3-issue-issueidorkey-worklog-get)

[See Jira API reference (get worklog by id)](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-issue-worklogs/#api-rest-api-3-worklog-list-post)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-961';

try {
    $issueService = new IssueService();
    
    // get issue's all worklog
    $worklogs = $issueService->getWorklog($issueKey)->getWorklogs();
    var_dump($worklogs);
    
    // get worklog by id
    $wlId = 12345;
    $wl = $issueService->getWorklogById($issueKey, $wlId);
    var_dump($wl);
    
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'testSearch Failed : '.$e->getMessage());
}

```

#### Add watcher to Issue 

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-addWatcher)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-961';

try {
    $issueService = new IssueService();
    
    // watcher's id
    $watcher = 'lesstif';
    
    $issueService->addWatcher($issueKey, $watcher);
    
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'add watcher Failed : '.$e->getMessage());
}
```

#### Remove watcher from Issue

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-removeWatcher)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\JiraException;

$issueKey = 'TEST-961';

try {
    $issueService = new IssueService();
    
    // watcher's id
    $watcher = 'lesstif';
    
    $issueService->removeWatcher($issueKey, $watcher);
    
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'add watcher Failed : '.$e->getMessage());
}
```

#### issue notify

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issue-notify)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\IssueService;
use JiraCloud\Issue\Notify;
use JiraCloud\JiraException;

$issueKey = 'TEST-961';

try {
    $issueService = new IssueService();

    $noti = new Notify();

    $noti->setSubject('notify test')
        ->setTextBody('notify test text body')
        ->setHtmlBody('<h1>notify</h1>test html body')
        ->sendToAssignee(true)
        ->sendToWatchers(true)
        ->sendToUser('lesstif', true)
        ->sendToGroup('temp-group')
    ;

    $issueService->notify($issueKey, $noti);
    
} catch (JiraCloud\JiraException $e) {
    $this->assertTrue(false, 'Issue notify Failed : '.$e->getMessage());
}
```
#### Create Issue Link

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issueLink-linkIssues)

The Link Issue Resource provides functionality to manage issue links.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\IssueLink\IssueLink;
use JiraCloud\IssueLink\IssueLinkService;
use JiraCloud\JiraException;

try {
    $il = new IssueLink();

    $il->setInwardIssue('TEST-258')
        ->setOutwardIssue('TEST-249')
        ->setLinkTypeName('Relates' )
        ->setComment('Linked related issue via REST API.');
            
    $ils = new IssueLinkService();

    $ret = $ils->addIssueLink($il);

} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

#### Get Issue LinkType

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/issueLinkType-getIssueLinkTypes)

Rest resource to retrieve a list of issue link types.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\IssueLink\IssueLinkService;
use JiraCloud\JiraException;

try {
    $ils = new IssueLinkService();

    $ret = $ils->getIssueLinkTypes();
    
    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```

#### Create User

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/user-createUser)

Create user. 
By default created user will not be notified with email. If password field is not set then password will be randomly generated.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\User\UserService;

try {
    $us = new UserService();

    // create new user
    $user = $us->create([
            'name'=>'charlie',
            'password' => 'abracadabra',
            'emailAddress' => 'charlie@atlassian.com',
            'displayName' => 'Charlie of Atlassian',
        ]);

    var_dump($user);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get User Info

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/user-getUser)

Returns a user.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\User\UserService;

try {
    $us = new UserService();

    $user = $us->get(['username' => 'lesstif']);

    var_dump($user);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Find Users

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/user-findUsers)

Returns a list of users that match the search string and/or property. 

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\User\UserService;

try {
    $us = new UserService();

    $paramArray = [
        'username' => '.', // get all users. 
        'startAt' => 0,
        'maxResults' => 1000,
        'includeInactive' => true,
        //'property' => '*',
    ];

    // get the user info.
    $users = $us->findUsers($paramArray);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Find Assignable Users

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/user-findAssignableUsers)

Returns a list of users that match the search string. 

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\User\UserService;

try {
    $us = new UserService();

    $paramArray = [
        //'username' => null,
        'project' => 'TEST',
        //'issueKey' => 'TEST-1',
        'startAt' => 0,
        'maxResults' => 50, //max 1000
        //'actionDescriptorId' => 1,
    ];

    $users = $us->findAssignableUsers($paramArray);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```


#### Find users by query

[See Jira API reference](https://developer.atlassian.com/cloud/jira/platform/rest/v2/#api-rest-api-2-user-search-query-get)

Returns a list of users that match the search string.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\User\UserService;

try {
    $us = new UserService();

    $paramArray = [
      'query' => 'is watcher of TEST',
    ];

    $users = $us->findUsersByQuery($paramArray);
    var_dump($users);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### delete User

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/user-removeUser)

Removes user.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\User\UserService;

try {
    $us = new UserService();

    $paramArray = ['username' => 'user@example.com'];

    $users = $us->deleteUser($paramArray);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### update User

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/user-updateUser)

Updates user.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\User\UserService;

try {
    $us = new UserService();

    $paramArray = ['username' => 'user@example.com'];

    // create new user
    $user = [
            'name'=>'charli',
            'password' => 'abracada',
            'emailAddress' => 'charli@atlassian.com',
            'displayName' => 'Charli of Atlassian',
        ];

    $updatedUser = $us->update($paramArray, $user)

    var_dump($updatedUser);

} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Create Group

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/group-createGroup)

Create new group.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\Group\GroupService;
use JiraCloud\Group\Group;

try {
    $g = new Group();

    $g->name = 'Test group for REST API';

    $gs = new GroupService();
    $ret = $gs->createGroup($g);

    var_dump($ret);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get Users from group

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/group-getUsersFromGroup)

returns a paginated list of users who are members of the specified group and its subgroups.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\Group\GroupService;

try {
   $queryParam = [
        'groupname' => 'Test group for REST API',
        'includeInactiveUsers' => true, // default false
        'startAt' => 0,
        'maxResults' => 50,
    ];

    $gs = new GroupService();

    $ret = $gs->getMembers($queryParam);

    // print all users in the group
    foreach($ret->values as $user) {
        print_r($user);
    }
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

### Add User to group

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/group-addUserToGroup)

add user to given group.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\Group\GroupService;

try {
    $groupName  = '한글 그룹 name';
    $userName = 'lesstif';

    $gs = new GroupService();

    $ret = $gs->addUserToGroup($groupName, $userName);

    // print current state of the group.
    print_r($ret);

} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

### Remove User from group

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/group-removeUserFromGroup)

Removes given user from a group.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\JiraException;
use JiraCloud\Group\GroupService;

try {
    $groupName  = '한글 그룹 name';
    $userName = 'lesstif';

    $gs = new GroupService();

    $gs->removeUserFromGroup($groupName, $userName);

} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get All Priority list

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/priority)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Priority\PriorityService;
use JiraCloud\JiraException;

try {
    $ps = new PriorityService();

    $p = $ps->getAll();
	
    var_dump($p);
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}
```

#### Get Priority

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/priority)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Priority\PriorityService;
use JiraCloud\JiraException;

try {
    $ps = new PriorityService();

    $p = $ps->get(1);
	
    var_dump($p);
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}
```

#### Get Attachment Info

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/attachment-getAttachment)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Attachment\AttachmentService;
use JiraCloud\JiraException;

try {
    $attachmentId = 12345;

    $atts = new AttachmentService();
    $att = $atts->get($attachmentId);

    var_dump($att);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```



Gets the attachment information and saves the attachment into the outDir directory.

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Attachment\AttachmentService;
use JiraCloud\JiraException;

try {
    $attachmentId = 12345;
    $outDir = 'attachment_dir';
    
    $atts = new AttachmentService();
    $att = $atts->get($attachmentId, $outDir, $overwrite = true);

    var_dump($att);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}
```


#### Remove attachment

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/attachment-removeAttachment)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Attachment\AttachmentService;
use JiraCloud\JiraException;

try {
    $attachmentId = 12345;

    $atts = new AttachmentService();

    $atts->remove($attachmentId);
} catch (JiraCloud\JiraException $e) {
	print('Error Occurred! ' . $e->getMessage());
}
```

#### Create version

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/version-createVersion)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Issue\Version;
use JiraCloud\Project\ProjectService;
use JiraCloud\Version\VersionService;
use JiraCloud\JiraException;

try {
    $projectService = new ProjectService();
    $project = $projectService->get('TEST');

    $versionService = new VersionService();

    $version = new Version();

    $version->setName('1.0.0')
            ->setDescription('Generated by script')
            ->setReleased(true)
            ->setReleaseDate(new \DateTime())
            ->setProjectId($project->id);

    $res = $versionService->create($version);

    var_dump($res);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Update version

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/version-updateVersion)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Version\VersionService;
use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $versionService = new VersionService();
    $projectService = new ProjectService();

    $ver = $projectService->getVersion('TEST', '1.0.0');

    // update version
    $ver->setName($ver->name . ' Updated name')
        ->setDescription($ver->description . ' Updated description')
        ->setReleased(false)
        ->setReleaseDate(
            (new \DateTime())->add(date_interval_create_from_date_string('1 months 3 days'))
        );

    $res = $versionService->update($ver);

    var_dump($res);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Delete version

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/version-delete)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Version\VersionService;
use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $versionService = new VersionService();
    $projectService = new ProjectService();

    $version = $projectService->getVersion('TEST', '1.0.0');

    $res = $versionService->delete($version);

    var_dump($res);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get version related issues

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/version-getVersionRelatedIssues)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Version\VersionService;
use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $versionService = new VersionService();
    $projectService = new ProjectService();

    $version = $projectService->getVersion('TEST', '1.0.0');

    $res = $versionService->getRelatedIssues($version);

    var_dump($res);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get version unresolved issues

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/version-getVersionUnresolvedIssues)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Version\VersionService;
use JiraCloud\Project\ProjectService;
use JiraCloud\JiraException;

try {
    $versionService = new VersionService();
    $projectService = new ProjectService();

    $version = $projectService->getVersion('TEST', '1.0.0');

    $res = $versionService->getUnresolvedIssues($version);

    var_dump($res);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Create component

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/component-createComponent)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Component\ComponentService;
use JiraCloud\Issue\Version;
use JiraCloud\Project\Component;
use JiraCloud\JiraException;

try {
    $componentService = new ComponentService();
    
    $component = new Component();
    $component->setName('my component')
              ->setDescription('Generated by script')
              ->setProjectKey('TEST');

    $res = $componentService->create($component);

    var_dump($res);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Update component

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/component-updateComponent)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Component\ComponentService;
use JiraCloud\Issue\Version;
use JiraCloud\Project\Component;
use JiraCloud\JiraException;

try {
    $componentService = new ComponentService();
    
    $component = $componentService->get(10000); // component-id
    $component->setName($component->name . ' Updated name')
              ->setDescription($component->description . ' Updated descrption')
              ->setLeadUserName($component->lead->key);  // bug in jira api

    $res = $componentService->update($component);

    var_dump($res);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

##### Delete component

[See Jira API reference](https://docs.atlassian.com/software/jira/docs/api/REST/latest/#api/2/component-deleteComponent)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Component\ComponentService;
use JiraCloud\Issue\Version;
use JiraCloud\Project\Component;
use JiraCloud\JiraException;

try {
    $componentService = new ComponentService();
    
    $component = $componentService->get(10000); // component-id

    $res = $componentService->delete($component);

    var_dump($res);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```


#### Get board list
[See Jira API reference](https://developer.atlassian.com/cloud/jira/software/rest/#api-rest-agile-1-0-board-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Board\BoardService;

try {
  $board_service = new BoardService();
  $board = $board_service->getBoardList();
  
  var_dump($board);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```
#### Get board info
[See Jira API reference](https://developer.atlassian.com/cloud/jira/software/rest/#api-rest-agile-1-0-board-boardId-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Board\BoardService;

try {
  $board_service = new BoardService();
  $board_id = 1;
  $board = $board_service->getBoard($board_id);
  
  var_dump($board);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get board issues
[See Jira API reference](https://developer.atlassian.com/cloud/jira/software/rest/#api-rest-agile-1-0-board-boardId-issue-get)

```php
<?php
require 'vendor/autoload.php';

use JiraCloud\Board\BoardService;

try {
  $board_service = new BoardService();
  $board_id = 1;
  $issues = $board_service->getBoardIssues($board_id, [
    'maxResults' => 500,
    'jql' => urlencode('status != Closed'),
  ]);
  
  foreach ($issues as $issue) {
    var_dump($issue);
  }
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get board epics
[See Jira API reference](https://developer.atlassian.com/cloud/jira/software/rest/#api-agile-1-0-board-boardId-epic-get)

```php
<?php
require 'vendor/autoload.php';

try {
  $board_service = new JiraCloud\Board\BoardService();
  $board_id = 1;
  $epics = $board_service->getBoardEpics($board_id, [
    'maxResults' => 500,
  ]);
  
  foreach ($epics as $epic) {
    var_dump($epic);
  }
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get epic info
[See Jira API reference](https://developer.atlassian.com/cloud/jira/software/rest/#api-agile-1-0-epic-epicIdOrKey-get)

```php
<?php
require 'vendor/autoload.php';

try {
  $epic_service = new JiraCloud\Epic\EpicService();
  $epic_id = 1;
  $epic = $epic_service->getEpic($epic_id);
  
  var_dump($epic);
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

#### Get epic issues
[See Jira API reference](https://developer.atlassian.com/cloud/jira/software/rest/#api-agile-1-0-epic-epicIdOrKey-issue-get)

```php
<?php
require 'vendor/autoload.php';

try {
  $epic_service = new JiraCloud\Epic\EpicService();
  $epic_id = 1;
  $issues = $epic_service->getEpicIssues($epic_id, [
    'maxResults' => 500,
    'jql' => urlencode('status != Closed'),
  ]);
  
  foreach ($issues as $issue) {
    var_dump($issue);
  }
} catch (JiraCloud\JiraException $e) {
    print('Error Occurred! ' . $e->getMessage());
}

```

# License

Apache V2 License

# JIRA Rest API Documents
* https://developer.atlassian.com/cloud/jira/platform/rest/v3/
