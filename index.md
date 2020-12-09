
### Overview:

The Jira to Azure DevOps work item migration tool lets you export data from Jira and import it as work items in Azure DevOps. The migration process is done in two phases, first data is exported from Jira to Json files. This can be done in several batches until the right set of items are ready for import. The second step imports the data files to work items in Azure DevOps.

The key capabilities of the tool include:

> - Jira items are exported based on JQL queries.
> -	Users can be translated during migration to maintain proper history.
> - Field mapping is provided to map fields from the source to target account based on configuration file.
> -	State mapping between Jira and Azure DevOps states.
> -	History from Jira is maintained.
> -	Dates such as created or changed date are maintained.
> -	Area/Iteration paths can be defaulted to a specific value when they don't exist in the target project.



<h3>Steps of Migration Process: </h3>

1.	Mapping of states and fields for projects in JIRA according to Azure DevOps Projects. 
2.	Configuration of projects in Azure DevOps according to the forms of JIRA.
3.	Configuring JSON file for migration which includes the state mapping and field mapping with the Field IDs of both JIRA and Azure DevOps.
4.	Exporting the work items to the local machine using migration tool like Solidify.
5.	Importing these work items to Azure DevOps using the same migration tool.
6.	Fixing the migrated data and Resolving the issues faced while migrating data.


<h3>Challenges faced during Migration:</h3>

1.	Limitation on Batch File Size-
While configuring the JSON File, it included a line of code which caused error in exporting of data from JIRA.
Code-"batch-size": 20,
While executing the JSON file for the first time and trying to export 3000 work items, we encountered an error where less work items were exported. There was count mismatch between the total number of work items in JIRA and work items exported. After some troubleshooting, we concluded to eliminate the line of code mentioned above and again tried executing the JSON file to export the work items.
After excluding the above line of code, all work items were exported. 

2.	Migrating Custom Field-
While analysing the fields in Jira work items there was system fields and custom fields. For system fields i.e., predefined field only names were to be mapped but for custom fields the Ids also needed to be mapped. When migration was in the initial stage this thing was not figured out. Only names were mapped in the file and migration was done. After some observation it was known that for custom fields the custom IDs need to be mapped with name so that the correct data can be exported. Later custom fields which were having drop down couldn’t migrate and it was fixed by latest version of Solidify. Then we were able to migrate the custom fields for work items in Azure DevOps.

3.	Export Log Failure-
While exporting the work items, the migrator tool use to stop working due to some connection issues, or work item in JIRA was unable to open, or attachment contained by JIRA work item. Initially, we solved this error by clearing all the exported data and log from the local machine and start with the migration all over again. But later on we started ordering the work items by the KEY, hence if any error was encountered while exporting the work items which caused the exporting to shut down then we could note down the last work item which was exported and make changes in the JSON file and continue exporting the remaining work items.

4.	JIRA Title more than 250 characters-
While importing work items from Jira to Azure DevOps, the work items having long title was not imported properly. While cross checking from Jira and Azure DevOps it was known that the maximum characters supported in migration was 250. Titles having characters more than 250 in Jira was not migrated, but it was blank in title field for those work items in Azure DevOps. There were cases where the titles for project was long or more than 250 in most of the work items. So, we had to identify the missing Ids. This process was carried out by looking for the missing revisions in log file which was generated while migrating from Jira to Azure DevOps. We had to look for the Ids of missing title in Azure DevOps by applying a query and then find the subsequent Jira Ids in the log file generated while migrating. Later we had to manually update the titles for the missing titles in Azure Ids and complete the work item.


5.	Unicode characters could not be exported-
In Jira work items there were some Unicode characters in some fields. Due to those Unicode characters in work items we were unable to complete the migration. Those Unicode characters use to appear something else in Azure DevOps. Later after some observation and talking to the Solidify team we concluded that the work items having Unicode characters cannot be migrated. So, we had to update those work items manually further in Azure DevOps.


6.	Migrating Custom Work items-
Initially when we started with the migration of custom work items like Tech Tickets, we were not able to migrate them directly. As the tool didn’t support the functionality so we had the option to migrate these work items into Product Backlog Item and then rename the Product Backlog Item to the desired work item name which is present in JIRA. Later, we could migrate custom work items directly using the migration tool after an update patch for the same was released.


7.	Bulk Updating Work items-
After migrating the work items from JIRA to Azure DevOps, if we have encountered any error and the data in JIRA doesn’t match with data in Azure DevOps then we need to rectify the data manually. So, if the field had incorrect data for many work items of the same type then we usually bulk updated this field by generating a query. We Bulk Updated the work items in Azure DevOps if we have created some new field in Azure DevOps which was not present in JIRA and the project team has instructed us to create the new field and fill the value according to some parameter.

8.	Failure to export Date-Time Picker Field-
In Jira some work items had one additional field Date-Time Picker which could not migrate to Azure DevOps. Date- Time Picker field displays blank in Azure DevOps after complete migration of project. To solve this issue for every specific project manual bulk update process was adapted. For example: If a project has some work items which have Date-Picker field then those IDs were identified in Jira and then were matched with the Azure DevOps title (which were having JIRA IDs). Those Ids having Date-Time picker was separated and was updated manually in bulk through query in Azure if same date is across those Ids.





9.	Manually created some work items-
Whenever we have encountered any of the above mention challenges and could not migrate the work items from JIRA to Azure DevOps after implementing all the possible solution, the only solution left was to create the data manually. We had to check the import log and list the missing data, after listing all the missing elements we created those elements in Azure DevOps by referring to the state and work item mapping. While creating the work item we needed to check if we have entered all the data and attached the links and attachments correctly.

10.	Storage Issue for Migrating Big JIRA projects -
Generally migrating projects from JIRA to Azure DevOps, we initiated the migrator and exported all the data in the local machine and then imported the same data onto Azure DevOps. But when exporting projects with 10000+ work items we encountered a storage space because the storage space would run out while exporting the data from JIRA. To solve this issue, we had to create a share drive for the project and export the data into the share drive by providing the path of share drive in JSON file and then import the data from the share drive into Azure DevOps. 

11.	Linking of work items across projects-
When exporting data from JIRA to Azure DevOps the linking between work items of the same projects was retained after migration but the linking of the work items across multiple projects was not reflected in Azure DevOps. There is no option to link two work items from different projects in Azure DevOps. To solve this issue, we had to combine these projects into a single project in Azure DevOps with guidance by the respective project team. And the migrate the data of all projects simultaneously into the single Azure project so that linking across projects is done properly and no linking between work items is lost in the process. But the solution we have implemented is a workaround and cannot be considered as a permanent solution for this problem because in case the project team does not want to merge the projects then linking cannot happen for work items across different projects in Azure DevOps.

12.	Migrating Time for Big JIRA projects-
When migrating projects in JIRA with 10000+ work items, it generally took days to export and import the data. While the exporting and importing of the data was taking place you had to continuously monitor the process in case of failure you needed to restart/continue it. In case it failed in between we had to edit the query with the Key of work item until which the exporting/importing was done. Migrating Big JIRA projects generally took a lot of time and effort.
13.	Jira iteration path: special charcter not migrated properly. Created a new field for this.




 




