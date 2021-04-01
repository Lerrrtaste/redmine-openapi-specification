

<h1 align="center">
  <br>
  <a href="https://github.com/Lerrrtaste/redmine-openapi-specification/blob/main/redlab_openapi_logo.png?raw=true"><img src="https://user-images.githubusercontent.com/22920627/112852229-0655e800-90ac-11eb-819f-15e7d9096ecb.png" alt="Markdownify" width="600"></a>
  <br>
  Redmine OpenAPI v2.0 Specification
  <br>
</h1>

<h4 align="center">OpenAPI (<a href="https://swagger.io/">Swagger</a>) Specification for the Redmine Rest API</h4>



<p align="center">
  <a href="#download">Download</a> <br>•<br>
  <a href="https://petstore.swagger.io/?url=https%3A%2F%2Fraw.githubusercontent.com%2FLerrrtaste%2Fredmine-openapi-specification%2Fmain%2Fredmine_openapi.yaml">View in Swagger UI</a><br> •<br>
  <a href="https://www.redmine.org/projects/redmine/wiki/Rest_api">Redmine API Documentation</a> <br>•<br>
  <a href="#implementation-progress">Implementation Progress</a> <br>•<br>
  <a href="#custom-connector">How To: Use as Custom Connector on Microsoft Power Platform</a> <br><br>
</p>

## Implementation Progress
| *Resource<br><br>*                      |          *Implemented<br><br>*         |
|:-----------------------------:|:----------------------------:|
<br><br>Stable:
| **Issues** (1.0)                  |               :heavy_check_mark:	              |
| **Projects** (1.0)                | :heavy_check_mark:<br>(except delete and create) |
| Users                         |               ❌              |
| **Time Entries** (1.1)            |               :heavy_check_mark:              |
<br><br>Alpha and below:
| Issue Statuses (1.3)          |               :heavy_check_mark:              |
| Trackers (1.3)                |               :heavy_check_mark:              |
| Project Memberships           |               ❌              |
| News                          |               ❌              |
| Issue Relations               |               ❌              |
| Versions                      |               ❌              |
| Wiki Pages                    |               ❌              |
| Queries                       |               ❌              |
| Attachments                   |               ❌              |
| Enumerations                  |               ❌              |
| Issue Categories              |               ❌              |
| Roles                         |               ❌              |
| Groups                        |               ❌              |
| Custom Fields                 |               ❌              |
| Search                        |               ❌              |
| Files                         |               ❌              |

## Download
[Go to the original YAML File](https://github.com/Lerrrtaste/redmine-openapi-specification/blob/dc9e239b80dd68d4fc57fb1e620a709fec0e76f6/redmine_openapi.yaml)
or
[the generated json.](https://github.com/Lerrrtaste/redmine-openapi-specification/blob/dc9e239b80dd68d4fc57fb1e620a709fec0e76f6/redmine_openapi.json) 

## Custom Connector
There are some I found limitations when using Custom Connectors that prevent the "correct" OpenAPI File from working.
1. It does not support Parameter definition at the Path scope.<br>-> That is no big problem, I just reference every repeating Parameter Object from the Parameters Definitions Object. (For example the IssueID Path Parameter: $ref: '#/parameters/idParam'<br><br>
2. Redmine requires the following Body Scheme for Updating/Creating Resources:  `{
  "issue": {
    "project_id": 1,
    "subject": "Example",
    "priority_id": 4,
    ...
  }
}` While some Parameters are required, most are optional. In the Custom Connector Editor's Test Tab this works fine, but when using it in an actual formula in Power Apps, it will throw an error if you don't set every single possible parameter of the JSON Object. But only for nested Objects, optional top level Parameters work as expected.  That means it is only possible to update *every* attribute of an Issue, and initally empty ones now need to have some value.

To get around this I collect everything as top level attribute and format it in Redmine's required format via Policies. But those cannot be shared in the file so you have to add them yourself:<br>- First import the [the customconnector.json file](https://github.com/Lerrrtaste/redmine-openapi-specification/blob/cef4523fe424ad3558504bccab0f1cfd094ee3b8/custom_connector/redmine_openapi_customconnector.json) as usual<br>- Then add a Policy for every Resource you want to Create/Update later using these Input Parameters:

| Input Parameter | Value                                                                     |
|-----------------|---------------------------------------------------------------------------|
| Template        | Set Property                                                              |
| Operations      | Update/Create operation IDs of the Resource<br>e.g. IssueCreate, IssueUpdate |
| Parent Path     | @body()                                                                   |
| Relative Path   | Name of the required top level JSON key <br>e.g. issue or time_entry                          |
| Value to add    | {@body()}                                                                 |
| Run on          | Request                                                                   |
