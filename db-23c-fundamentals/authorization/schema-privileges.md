# Capturing Schema Privilege Use

## Introduction

*This LiveLab is based on the 23c feature 46886-1, Schema Privileges. Privilege analysis has been around for a while but works nicely with this feature.*
This lab shows how to capture a user's schema privilege use for the <code>SELECT ANY TABLE</code> and <code>DELETE ANY TABLE</cod> system privileges on the <cod>HR</code> schema.

Estimated Time: 15 minutes

### About Schema Privileges and Privilege Analysis
A schema privilege is a system privilege that is granted to a user and is restricted to the objects in a specified schema. For example, the following schema privilege grant enables user <code>pfitch</code> to select and delete tables in the <code>HR</code> schema, but not in any other schema.

<code>GRANT SELECT ANY TABLE, DELETE ANY TABLE ON SCHEMA HR TO pfitch;</code>

Privilege analysis enables a database administrator to capture a user's privilege use and then generate a report that describes how the user is using the privilege.

### Objectives

In this lab, you will:
* Create an administrator user and a user who will have schema privileges.
* Create and enable a privilege analysis policy that will track the schema privileged user's activities.
* Have the schema privileged user perform an activity.
* Disable the privilege analysis policy after the user performed the activity.
* Generate and view the privilege analysis report on the user.

### Prerequisites (Optional)

This lab assumes you have:
* An Oracle account
* An understanding of Oracle Database privileges, but in-depth knowlege is not needed

## Task 1: <what is the action in this step>

(optional) Step 1 opening paragraph.

1. Sub step 1

		![Image alt text](images/sample1.png)

  To create a link to local file you want the reader to download, use the following format.

	> **Note:** _The filename must be in lowercase letters and CANNOT include any spaces._

  Download the [starter file](files/starter-file.sql) SQL code.

	When the file type is recognized by the browser, it will attempt to render it. So you can use the following format to force the download dialog box.

	> **Note:** _The filename must be in lowercase letters and CANNOT include any spaces._

	Download the [sample JSON code](files/sample.json?download=1).

  *IMPORTANT: do not include zip files, CSV, PDF, PSD, JAR, WAR, EAR, bin or exe files - you must have those objects stored somewhere else. We highly recommend using Oracle Cloud Object Store and creating a PAR URL instead. See [Using Pre-Authenticated Requests](https://docs.cloud.oracle.com/en-us/iaas/Content/Object/Tasks/usingpreauthenticatedrequests.htm)*

2. Sub step 2

    ![Image alt text](images/sample1.png)

4. Example with inline navigation icon ![Image alt text](images/sample2.png) click **Navigation**.

5. Example with bold **text**.

  If you add another paragraph, add 3 spaces before the line.

6. Example with bold **text**.

    If you add another paragraph, add 3 spaces before the line.  

## Task 2: <what is the action in this step>

1. Sub step 1 - tables sample

  Use tables sparingly:

  | Column 1 | Column 2 | Column 3 |
  | --- | --- | --- |
  | 1 | Some text or a link | More text  |
  | 2 |Some text or a link | More text |
  | 3 | Some text or a link | More text |

2. You can also include bulleted lists - make sure to indent 4 spaces:

    - List item 1
    - List item 2

3. Code examples

    ```
    Adding code examples
  	Indentation is important for the code example to appear inside the step
    Multiple lines of code
  	<copy>Enclose the text you want to copy in <copy></copy>.</copy>
    ```

4. Code examples that include variables

	```
  <copy>ssh -i <ssh-key-file></copy>
  ```

## Learn More

*(optional - include links to docs, white papers, blogs, etc)*

* [URL text 1](http://docs.oracle.com)
* [URL text 2](http://docs.oracle.com)

## Acknowledgements
* **Author** - <Name, Title, Group>
* **Contributors** -  <Name, Group> -- optional
* **Last Updated By/Date** - <Name, Group, Month Year>
