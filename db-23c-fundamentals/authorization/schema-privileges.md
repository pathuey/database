# Capturing Schema Privilege Use

## Introduction

*REVIEWERS: This LiveLab is based on the 23c feature 46886-1, Schema Privileges. Privilege analysis has been around for a while but has been updated to accommodate this feature.*

This lab shows how to capture a user's schema privilege use for the <code>SELECT ANY TABLE</code> and <code>DELETE ANY TABLE</code> system privileges on the <code>HR</code> schema.

Estimated Time: 15 minutes

### About Schema Privileges and Privilege Analysis
A schema privilege is a system privilege that is granted to a user and is restricted to the objects in a specified schema. By granting the system privilege to a user for the entire schema, you simplify application authorizations without compromising security. For example, the following schema privilege grant enables user <code>pfitch</code> to select and delete tables in the <code>HR</code> schema, but not in any other schema.

<code>GRANT SELECT ANY TABLE, DELETE ANY TABLE ON SCHEMA HR TO pfitch;</code>

Privilege analysis enables a database administrator to capture a user's privilege use and then generate a report that describes how the user is using the privilege. Privilege analysis, which was introduced in Oracle Database release 12c, has been enhanced to accommodate schema privileges.

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

1. Log into a PDB as a user who has the <code>CREATE USER</code> system privilege.

   For example:

   <pre>
   sqlplus sec_admin@<i>pub_name</i>  
   Enter password: <i>password</i>
   </pre>

   To find the available PDBs, query the <code>DBA_PDBS</code> data dictionary  view. To check the current PDB, run the <code>show con_name</code>. command.

2. Create the following users:

   <copy>
   <pre>
   CREATE USER pa_admin IDENTIFIED BY <i>password</i>;
   CREATE USER sec_user IDENTIFIED BY <i>password</i>;
   </pre>
   </copy>
   Replace <code><i>password</i></code> with a password that is secure.

4. Connect as a user who has the privileges to grant roles and system privileges to other users, and who has been granted the owner authorization for the Oracle System Privilege and Role Management realm. (User <code>SYS</code> has these privileges by default.)

   For example:
   <pre>
   CONNECT dba_psmith@<i>pub_name</i>
   Enter password: <i>password</i>
   </pre>
   In SQL*Plus, a user who has been granted the <code>DV_OWNER</code> role can check the authorization by querying the <code>DBA_DV_REALM_AUTH</code> data dictionary view. To grant the user authorization, use the <code>DBMS_MACADM.ADD_AUTH_TO_REALM</code> procedure.

5. Grant the following roles and privileges to the users.

   <copy>
   ```
   GRANT CREATE SESSION, CAPTURE_ADMIN TO pa_admin;  
   GRANT CREATE SESSION TO sec_user;
   ```
   </copy>
   User <code>pa_admin</code> will create the privilege analysis policy that will analyze the database tuning operations that user <code>sec_user</code> will perform.

6. For user <code>sec_user</code>, grant the <code>SELECT ANY TABLE</code> and <code>DELETE ANY TABLE</code> system privileges as schema privileges for the <code>HR</code> schema.
    <copy>
    ```
    GRANT SELECT ANY TABLE, DELETE ANY TABLE ON SCHEMA HR TO sec_user;
    ```
    </copy>
## Task 2: Create and Enable a Privilege Analysis Policy

   User <code>pa_admin</code> must create the and enable the privilege analysis policy.

1. Connect to the PDB as user <code>pa_admin</code>.

   <pre>
   CONNECT pa_admin@<i>pub_name</i>
   Enter password: <i>password</i>
   </pre>

2. Create the following privilege analysis policy:
   <copy>
   ```
   BEGIN  
     DBMS_PRIVILEGE_CAPTURE.CREATE_CAPTURE(  
       name          => 'sec_user_capture_pol',  
       description   => 'Captures sec_user used and not used privileges',  
       type          => DBMS_PRIVILEGE_CAPTURE.G_DATABASE);  
   END;  
   /
   ```
   </copy>
   In this example, <code>type</code> specifies that the type is a database wide condition.

3. Enable the policy.
   <copy>
   ```
   EXEC DBMS_PRIVILEGE_CAPTURE.ENABLE_CAPTURE ('sec_user_capture_pol');
   ```
   </copy>
   At this point, the policy is ready to start recording the actions of user <code>sec_user</code>.

## Task 3: Use the READ ANY TABLE System Privilege

   User <code>sec_user</code> uses the <code>SELECT ANY TABLE</code> system privilege on the <code>HR</code> schema.

1. Connect as user <code>sec_user</code>.

   <pre>
   CONNECT sec_user@<i>pub_name</i>  
   Enter password: <i>password</i>
   </pre>

2. Query the <code>HR.EMPLOYEES</code> table to find <code>sec_user</code>'s highest paid coworkers.
   <copy>
   ```
   SELECT FIRST_NAME, LAST_NAME FROM HR.EMPLOYEES WHERE SALARY > 8000;
   ```
   </copy>
   Output similar to the following appears:

   ```  
   FIRST_NAME           LAST_NAME  
   -------------------- -------------------------  
   Steven               King  
   Neena                Kochhar  
   Lex                  De Haan  
   Alexander            Hunold  
   Nancy                Greenberg  
   Daniel               Faviet  
   ...      
   ```

## Task 4: Disable the Privilege Analysis Policy

   You must disable the policy before you can generate a report that captures the actions of user <code>sec_user</code>.

1. Connect as user <code>pa_admin</code>.

   <pre>
   CONNECT pa_admin@<i>pub_name</i>  
   Enter password: <i>password</i>
   </pre>

2. Disable the <code>sec_user_capture_pol</code> privilege policy.
   <copy>
   ```
   EXEC DBMS_PRIVILEGE_CAPTURE.DISABLE_CAPTURE ('sec_user_capture_pol');
   ```
   </copy>
## Task 5: Generate and View Privilege Analysis Reports

   With the privilege analysis policy disabled, user <code>pa_admin</code> can generate and view privilege analysis reports.

1. As user <code>pa_admin</code>, generate the privilege analysis results.
   <copy>
   ```
   EXEC DBMS_PRIVILEGE_CAPTURE.GENERATE_RESULT ('sec_user_capture_pol');
   ```
   </copy>
   The generated results are stored in the privilege analysis data dictionary views.

2. Enter the following commands to format the data dictionary view output:
   <copy>
   ```
   col sch_priv format a20  
   col schema format a20
   ```
   </copy>
3. Query the <code>DBA_USED_SCHEMA_PRIVS</code> data dictionary view to find the schema privileges that user <code>sec_user</code> used during the privilege analysis period.
   <copy>
   ```
   SELECT SCH_PRIV, SCHEMA FROM DBA_USED_SCHEMA_PRIVS WHERE USERNAME = 'SEC_USER';
   ```
   </copy>
   The following output should appear:

   ```
   SCH_PRIV          SCHEMA  
   –---------------  –---------------  
   SELECT ANY TABLE  HR  
   ```

4. Query <code>DBA_UNUSED_SCHEMA_PRIVS</code> to find the unused schema privileges for <code>user sec_user</code>.
   <copy>
   ```
   SELECT SCH_PRIV, SCHEMA FROM DBA_UNUSED_SCHEMA_PRIVS WHERE USERNAME = 'SEC_USER';
   ```
   </copy>
   The following output should appear:

   ```
   SCH_PRIV          SCHEMA  
   –---------------  –---------------  
   DELETE ANY TABLE  HR  
   ```

## Task 6: Remove the Components for This Lab

   You can remove the components that you created for this lab if you no longer need them.

1. As user <code>pa_admin</code>, drop the <code>sec_user_capture_pol</code> privilege analysis policy.
   <copy>
   ```
   EXEC DBMS_PRIVILEGE_CAPTURE.DROP_CAPTURE ('sec_user_capture_pol');
   ```
   </copy>
   Even though in the next steps you will drop the <code>pa_admin</code> user, including any objects that were created in this user's schema, you must manually drop the <code>sec_user_capture_pol</code> privilege analysis policy because this object resides in the <code>SYS</code> schema.

2. Connect as the user who created the user accounts.

   For example:

   <pre>
   CONNECT sec_admin@<i>pub_name</i>
   Enter password: <i>password</i>
   </pre>

3. Drop the users <code>pa_admin</code> and <code>sec_user</code>.
   <copy>
   ```
   DROP USER pa_admin CASCADE;  
   DROP USER sec_user;
   ```
   </copy>
## Learn More
*REVIEWERS: The following links go to the 23c internal draft of DBSEG but will be replaced with public library links once 23c is released. Should it have the name of the manual or are we going only by topic names now?*

* [Managing Schema Privileges](http://st-doc.us.oracle.com/id_common/review/docbuilder/html/F46690_01/configuring-privilege-and-role-authorization.htm#GUID-483D04AF-BC5B-4B3D-9D9A-1D2C3CE8F12F)
* [Administering Schema Security Policies](http://st-doc.us.oracle.com/id_common/review/docbuilder/html/F46690_01/configuring-privilege-and-role-authorization.htm#GUID-DBDFDCE7-5E6E-43A8-9D21-87388B2AC179)
* [Performing Privilege Analysis to Find Privilege Use](http://st-doc.us.oracle.com/id_common/review/docbuilder/html/F46690_01/GUID-44CB644B-7B59-4B3B-B375-9F9B96F60186.htm)

## Acknowledgements
* **Author** - Patricia Huey, Consulting User Assistance Developer, User Assistance Development
* **Last Updated By/Date** - Patricia Huey, User Assistance Development, July 2022
