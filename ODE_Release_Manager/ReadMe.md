#ODE Release Management SSIS project#

ODE Release Manager enables ODE Configuration code promotion without writing release script into the file system.
Multiple parameters help with the release customisation. Release manager could create a release record, build or re-build configuration release script, move release script into the target release environment and apply it to the target environment. Although new configuration is applied to the target environment, it is not applied to the physical Data Vault automatically. Run the following script to build new DV objects or re-build existing ones if configuration has been changed. Note that re-building the DV object means dropping and re-creating the table, all the data will be dropped.
https://github.com/OptimalBI/optimal-data-engine-mssql/wiki/How-to-Create-Missing-Vault-Objects


ODE release mechanism is described here:
https://github.com/OptimalBI/optimal-data-engine-mssql/wiki/Configuration-Release-Management

Note that this package releases ODE configuration code only. Staging code is not promoted by this SSIS package and should be released separately.

## Current functionality: ##
Details of the current functionality can be found here https://github.com/OptimalBI/optimal-data-engine-mssql/wiki/Release-Config-with-SSIS-package

## Notes ##
* SSIS package has been implemented in Visual Studio 2015.

## Installation ##
Deploy SSIS project to the release target environment. E.g. if you are going to release ODE configuration from DEV to TEST server, deploy release project into the TEST SQL Server Intergation Services catalog. Also installation require SQL server login "ODERelease" to be created on the source environment. This login should have read and write access to the source ODE Config, because the release SSIS package could build (compile) release script and write it into the table.

SSIS Project could be deployed via Visual Studio. You need to have Visual Studio 2015 with Data Tools extension for Integration Services installed on your computer.
* Open the solution in Visual Studio
* Right click on the project in Solution Explorer
* Select "Deploy"
* Provide server name where the project will be deployed and SSIS catalog folder name. If it is the first SSIS project on this server, it will prompt to initialise SSIS catalog first. It could be done from SQL Server Management studio. Right click on Integration Services Catalogs and choose "Create Catalog".
* Deploy SSIS package

You can provide all the parameters manually when executing the package. However, we recommend using SSIS project environment for the best experience. Set up an environment for Full release and an environment for Partial release in the SQL Server Integration Services catalog. In the SQL Server Management Studio navigate to the Integration Services Catalog on the server where the release SSIS package is installed. The folder contains two subfolders, Projects and Environments. Right click on Environments and select Create Environment.
Environment parameters are listed below.

##Full release environment##

----------------------------------------------------------------------------------------------------------------------
Parameter Name             | Type    | Description                                          | Value      | Sensitive |
----------------------------------------------------------------------------------------------------------------------
doApplyRelease             | Boolean | True if you wish the package to apply the release at | True       |           |
                           |         | the Object Vault. If FullRelease is true, This will  |            |           |
                           |         | Replace ALL Object Configuration.                    |            |           |
----------------------------------------------------------------------------------------------------------------------
doBuildRelease             | Boolean | True if you wish the package to Build the Release at | True       |           |
                           |         | Source before moving it to the Object Vault. If      |            |           |
                           |         | FullRelease is True, this will build a Full release! |            |           |
----------------------------------------------------------------------------------------------------------------------
doCopyRelease              | Boolean | True if you wish to copy a release from Source to    | True       |           |
                           |         | Object.                                              |            |           |
----------------------------------------------------------------------------------------------------------------------
doCreateReleaseHeader      | Boolean | If True, the package will attempt to Create your     | True       |           |
                           |         | release Header at Source                             |            |           |
----------------------------------------------------------------------------------------------------------------------
FullRelease                | Boolean | True if you wish to do a Full release.               | True       |           |
----------------------------------------------------------------------------------------------------------------------
Object_Config_Database     | String  | Config Database to which Config will be Applied.     | ODE_Config |           |
----------------------------------------------------------------------------------------------------------------------
Object_ServerName          | String  | Server where Config Database exists, to which Config | TestServer |           |
                           |         | will be Applied.                                     |            |           |
----------------------------------------------------------------------------------------------------------------------
Release_Reference_Source   | String  | Source of Reference - e.g. Jira                      | Jira       |           |
----------------------------------------------------------------------------------------------------------------------
Source_Config_Database     | String  | Config Database from which Config will be Sourced.   | ODE_Config |           |
----------------------------------------------------------------------------------------------------------------------
Source_Connection_Password | String  | Password used for accessing the Release source       | Pwd123     | *         |
----------------------------------------------------------------------------------------------------------------------
Source_ServerName          | String  | Server where Config Database exists, from which      | DevServer  |           |
                           |         | Config will be Sourced.                              |            |           |
----------------------------------------------------------------------------------------------------------------------

##Partial release environment##

----------------------------------------------------------------------------------------------------------------------
Parameter Name             | Type    | Description                                          | Value      | Sensitive |
----------------------------------------------------------------------------------------------------------------------
doApplyRelease             | Boolean | True if you wish the package to apply the release at | True       |           |
                           |         | the Object Vault. If FullRelease is true, This will  |            |           |
                           |         | Replace ALL Object Configuration.                    |            |           |
----------------------------------------------------------------------------------------------------------------------
doBuildRelease             | Boolean | True if you wish the package to Build the Release at | True       |           |
                           |         | Source before moving it to the Object Vault. If      |            |           |
                           |         | FullRelease is True, this will build a Full release! |            |           |
----------------------------------------------------------------------------------------------------------------------
doCopyRelease              | Boolean | True if you wish to copy a release from Source to    | True       |           |
                           |         | Object.                                              |            |           |
----------------------------------------------------------------------------------------------------------------------
doCreateReleaseHeader      | Boolean | If True, the package will attempt to Create your     | False      |           |
                           |         | release Header at Source                             |            |           |
----------------------------------------------------------------------------------------------------------------------
FullRelease                | Boolean | True if you wish to do a Full release.               | False      |           |
----------------------------------------------------------------------------------------------------------------------
Object_Config_Database     | String  | Config Database to which Config will be Applied.     | ODE_Config |           |
----------------------------------------------------------------------------------------------------------------------
Object_ServerName          | String  | Server where Config Database exists, to which Config | TestServer |           |
                           |         | will be Applied.                                     |            |           |
----------------------------------------------------------------------------------------------------------------------
Release_Reference_Source   | String  | Source of Reference - e.g. Jira                      | Jira       |           |
----------------------------------------------------------------------------------------------------------------------
Source_Config_Database     | String  | Config Database from which Config will be Sourced.   | ODE_Config |           |
----------------------------------------------------------------------------------------------------------------------
Source_Connection_Password | String  | Password used for accessing the Release source       | Pwd123     | *         |
----------------------------------------------------------------------------------------------------------------------
Source_ServerName          | String  | Server where Config Database exists, from which      | DevServer  |           |
                           |         | Config will be Sourced.                              |            |           |
----------------------------------------------------------------------------------------------------------------------

Configure SSIS project to use these environments. To do that, right click on the Release project, navigate to the Reference tab. On this screen, add references to the both environments. On the parameters tab hook up environment parameters. To do that, click on "..." button on the right hand side of the parameter line, choose "Use environment variable" and choose a variable from the drop-down list.
Once configured, use one of these environments when executing the Release package from the SQL Server Integration Services catalog for the default parameters or change them to those which suit your release scenario the best.

#Execution#

Right click on the package in the SQL Server Integration services catalog, choose "Execute". Select the environment from the drop-down list. This will populate most of the parameters with the predefined values. Provide the release number and release description in case if you are inserting the new header; or just the release number in case if the header exists already. Once execution has started, you will be suggested with monitoring the load via the execution report. Package execution shouldn't take long. Refresh the report if execution is still running. Once execution has succeeded, configuration is released to the target server. 
In case of failure, follow the links on the report to open all messages report for the error message.

## Feedback, suggestions, bugs, contributions: ##
Please submit these to GitHub issue tracking or join us in developing by forking the project and then making a pull request!

## Find out more: ##
Visit http://ode.ninja/ - this is where we keep our guides and share our knowledge. To find out more about OptimalBI and the work we do visit http://www.optimalbi.com or check out our blogs at http://optimalbi.com/blog/tag/data-vault/ for all the latest on our Data Vault journey. If you want to get in touch, you can email us at hey@optimalbi.com
