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

### Prerequisites

This lab assumes you have:
* An Oracle account
* An understanding of Oracle Database privileges, but in-depth knowlege is not needed

## Task 1: Create User Accounts

You must create two users, one to create the privilege analysis policy and a second user whose schema privilege use will be analyzed.

1. Log into a PDB as a user who has the CREATE USER system privilege.

   For example:

   <code>sqlplus sec_admin@<i>pdb_name</i>
   Enter password: <i>password</i></code>

   To find the available PDBs, query the <code>DBA_PDBS</code> data dictionary  view. To check the current PDB, run the <code>show con_name</code>. command.

2. Create the following users:

  <code>CREATE USER pa_admin IDENTIFIED BY <i>password</i>;
  CREATE USER sec_user IDENTIFIED BY <i>password</i>;</code>

  Replace <code><i>password</i></code> with a password that is secure.

4. Connect as a user who has the privileges to grant roles and system privileges to other users, and who has been granted the owner authorization for the Oracle System Privilege and Role Management realm. (User <code>SYS</code> has these privileges by default.)

   For example:

   <code>CONNECT dba_psmith@pdb_name
   Enter password: <i>password</i> </code>

   In SQL*Plus, a user who has been granted the <code>DV_OWNER</code> role can check the authorization by querying the <code>DBA_DV_REALM_AUTH</code> data dictionary view. To grant the user authorization, use the <code>DBMS_MACADM.ADD_AUTH_TO_REALM</code> procedure.

5. Grant the following roles and privileges to the users.

   <code>GRANT CREATE SESSION, CAPTURE_ADMIN TO pa_admin;
   GRANT CREATE SESSION TO sec_user;</code>

   User <code>pa_admin</code> will create the privilege analysis policy that will analyze the database tuning operations that user <code>sec_user</code> will perform.

6. For user <code>sec_user</code>, grant the <code>SELECT ANY TABLE</code> and <code>DELETE ANY TABLE</code> system privileges as schema privileges for the <code>HR</code> schema.

    <code>GRANT SELECT ANY TABLE, DELETE ANY TABLE ON SCHEMA HR TO sec_user;</code>  

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
