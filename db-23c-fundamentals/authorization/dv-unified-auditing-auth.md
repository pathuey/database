# Enabling Unified Auditing Authorization in an Oracle Database Vault Environment
*REVIEWERS: This LiveLab is based on the 23c feature 85595-1, Ability to Control Authorizations for Unified Auditing*

## Introduction

This LiveLab explores how to authorize an Oracle Database user to create unified audit policies in an Oracle Database Vault environment.

Estimated Time: 15 minutes

### About Unified Auditing Authorization in an Oracle Database Vault Environment)

In earlier releases of Oracle Database, an Oracle Database Vault user had to create command rules if they wanted to use audit-related PL/SQL statements such as <code>CREATE AUDIT POLICY</code>. Starting in release 23c, an Oracle Database Vault admnistrator (that is, a user who has the <code>DV_OWNER</code> or <code>DV_ADMIN</code> role) can authorize users in Database Vault to have the <code>AUDIT_ADMIN</code> or <code>AUDIT_VIEWER</code> role privileges, similar to authorizing Oracle Data Pump users or Oracle Database Replay users to work in an Oracle Database Vault environment.

### Objectives

In this lab, you will:
* Log in to SQL*Plus as a user who has been granted the <code>DV_OWNER</code> role.
* As this user, authorize user <code>HR</code> user to have unified auditing authorization.
* Connect as <code>HR</code> and create and drop a unified audit policy.
* Connect as the <code>DV_OWNER</code> user and then revoke unified auditing authorization from <code>HR</code>.

### Prerequisites (Optional)

This lab assumes you have:
* An Oracle account in a database that has Oracle Database Vault enabled
* Access to the HR schema
* Familiarity with Oracle Database Vault


## Task 1: Ensure That Oracle Database Vault Is Enabled

1. Log into a PDB as a user <code>SYSTEM</code>.

   <pre>
	 sqlplus system@<i>pdb_name</i>
	 Enter password: <i>password</i>
   </pre>

2. Execute the following query:

   <pre>
     SELECT * FROM DBA_DV_STATUS;
   </pre>

	 The output should include the following lines:

	 <pre>
	 DV_CONFIGURE_STATUS  TRUE
	 DV_ENABLE_STATUS     TRUE
   </pre>

	 If the output is <code>FALSE</code>, then you must enable Oracle Database Vault. See [Registering Oracle Database Vault](http://st-doc.us.oracle.com/id_common/review/docbuilder/html/F46691_01/getting-started-with-oracle-database-vault.htm#GUID-68558E32-ABD0-4495-8677-4F9E09283E5D).


## Task 2: Authorize User <code>HR</code> to Create Unified Audit Policies

1. Connect to the PDB as a user who has been granted the <code>DV_OWNER</code> role.

   For example:

	 <pre>
	 CONNECT dba_debra@<i>pdb_name</i>
	 Enter password: <i>password</i>
   </pre>

2. Execute the <code>DVSYS.DBMS_MACADM.AUTHORIZE_AUDIT_ADMIN</code> to grant the <code>HR</code> user auditing authorization.

   <pre>
     EXEC DBMS_MACADM.AUTHORIZE_AUDIT_ADMIN ('HR');
   </pre>

3. To ensure that <code>HR</code> has been granted authorization, query the <code>DBA_DV_AUDIT_ADMIN_AUTH</code> data dictionary view.

   <pre>
     SELECT * FROM DBA_DV_AUDIT_ADMIN_AUTH WHERE GRANTEE = 'HR';
   </pre>

	 User <code>HR</code> should appear in the output.

## Task 3: Connect as <code>HR</code> and Create a Simple Unified Audit Policy, then Drop This Policy

1. Connect as user <code>HR</CODE>.

   <pre>
	 CONNECT HR@<i>pdb_name</i>
	 Enter password: <i>password</i>
   </pre>

2. Create the following unified audit policy, which is specific to Oracle Database Vault.

   <pre>
	 CREATE AUDIT POLICY dv_realm_hr
     ACTIONS SELECT, UPDATE, DELETE
     ACTIONS COMPONENT=DV Realm Violation ON "Oracle Database Vault";
   </pre>

3. Next, enable the unified audit policy.
   <pre>   
	 AUDIT POLICY dv_realm_hr;
   </pre>

	 Even though the <code>HR</code> user is not an administrative user, <code>HR</code> is now able to create and enable audit policies.

4. User <code>HR</code> does not really need this policy, so disable and then drop the policy. 	 	 

   <pre>
	 NOAUDIT POLICY dv_realm_hr;
     DROP AUDIT POLICY dv_realm_hr;
   </pre>

## Task 4: Connect as the <code>DV_OWNER</code> User and Revoke the Unified Audit Authorization   

1. Connect as the <code>DV_OWNER</code> user.

   For example:

   <pre>
	 CONNECT dba_debra@<i>pdb_name</i>
	 Enter password: <i>password</i>
   </pre>

2. Revoke the unified audit authorization from the <code>HR</code> user.

   <pre>
     EXEC DBMS_MACADM.UNAUTHORIZE_AUDIT_ADMIN ('HR');
   </pre>

	 Now, user <code>HR</code> can no longer create and manage unified audit policies.

## Learn More

*REVIEWERS: The following link goes to the 23c internal draft of DVADM but will be replaced with the public library link once 23c is released.*

* [Using Oracle Database Auditing with Oracle Database Vault](http://st-doc.us.oracle.com/id_common/review/docbuilder/html/F46691_01/dba-operations-in-an-oracle-database-vault-environment.htm#GUID-389D36D3-32F8-4451-965E-76448CDBBB43)

## Acknowledgements
* **Author** - Patricia Huey, Consulting User Assistance Developer, User Assistance Development
* **Last Updated By/Date** - Patricia Huey, User Assistance Development, July 2022
