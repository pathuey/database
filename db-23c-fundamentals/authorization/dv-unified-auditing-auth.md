# Enabling Unified Auditing Authorization in an Oracle Database Vault Environment
*REVIEWERS: This LiveLab is based on the 23c feature 85595-1, Ability to Control Authorizations for Unified Auditing*

## Introduction

This LiveLab explores how to authorize an Oracle Database user to create unified audit policies in an Oracle Database Vault environment.

Estimated Time: 15 minutes

### About Unified Auditing Authorization in an Oracle Database Vault Environment

In earlier releases of Oracle Database, an Oracle Database Vault user had to create command rules if they wanted to use audit-related PL/SQL statements such as <code>CREATE AUDIT POLICY</code>. Starting in release 23c, an Oracle Database Vault admnistrator (that is, a user who has the <code>DV_OWNER</code> or <code>DV_ADMIN</code> role) can authorize users in Database Vault to have the <code>AUDIT_ADMIN</code> or <code>AUDIT_VIEWER</code> role privileges, similar to authorizing Oracle Data Pump users or Oracle Database Replay users to work in an Oracle Database Vault environment.

### Objectives

In this lab, you will:
* Log in to SQL*Plus as user <code>SYS</code> and then grant the <code>AUDIT_ADMIN</code> to user <code>HR</code> in a PDB.
* As user <code>HR</code>, try to create a unified audit policy.
* Connect as a user who has been granted the <code>DV_OWNER</code> role, and then authorize <code>HR</code> user to have unified auditing authorization.
* Connect as <code>HR</code> and try creating a unified audit policy again.
* Connect as the <code>DV_OWNER</code> user and then revoke unified auditing authorization from <code>HR</code>.
* Connect as user <code>SYS</code> and then revoke the <code>AUDIT_ADMIN</code> from <code>HR</code>.

### Prerequisites

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

   To find a list of available PDBs, query the <code>DBA_PDBS</code> data dictionary view:

   <pre>
     SELECT PDB_NAME FROM DBA_PDBS;
   </pre>

   To check the current PDB, run the <code>show con_name</code> command.

2. Execute the following query:

   <pre>
     SELECT * FROM DBA_DV_STATUS;
   </pre>

	 The output should include the following lines:

	 <pre>
	 DV_CONFIGURE_STATUS  TRUE
	 DV_ENABLE_STATUS     TRUE
   </pre>

	 If the output is <code>FALSE</code>, then you must enable Oracle Database Vault. See [Registering Oracle Database Vault](http://st-doc.us.oracle.com/id_common/review/docbuilder/html/F46691_01/getting-started-with-oracle-database-vault.htm#GUID-68558E32-ABD0-4495-8677-4F9E09283E5D). You will need to register Oracle Database Vault in the CDB root, and for the PDB in which you want to work.

## Task 2: Grant the AUDIT_ADMIN Role to User HR

1. Connect as user <code>SYS</code> to the PDB where user <code>HR</code> resides.

   <pre>
	 sqlplus sys@<i>pdb_name</i> as sysdba
	 Enter password: <i>password</i>
   </pre>

2. Grant the <code>AUDIT_ADMIN</code> role to <code>HR</code>.

   <pre>
     GRANT AUDIT_ADMIN TO HR;
   </pre>

## Task 3: As User HR, Try Creating a Unified Audit Policy

1. Connect to the PDB as user <code>HR</code>.

   <pre>
   CONNECT HR@<i>pdb_name</i>
   Enter password: <i>password</i>
   </pre>

2. Try to create a unified audit policy.

   <pre>
   CREATE AUDIT POLICY dv_realm_hr
   ACTIONS SELECT, UPDATE, DELETE
   ACTIONS COMPONENT=DV Realm Violation ON "Oracle Database Vault";
   </pre>

   The following error message appears:

   <pre>   
   ERROR at line 1:
   ORA-01031: insufficient privileges
   </pre>

   Alas, even though user <code>HR</code> has the <code>AUDIT_ADMIN</code>, this user cannot create unified audit policies in Oracle Database Vault.

## Task 4: Authorize User HR to Create Unified Audit Policies

1. Connect to the PDB as a user who has been granted the <code>DV_OWNER</code> role.

   For example:

	 <pre>
	 CONNECT c##sec_admin_owen@<i>pdb_name</i>
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

	 User <code>HR</code> should appear in the output for the <code>GRANTEE</code> column.

## Task 5: Connect as HR and Try Creating the Audit Policy Again

1. Connect as user <code>HR</CODE>.

   <pre>
	 CONNECT HR@<i>pdb_name</i>
	 Enter password: <i>password</i>
   </pre>

2. Try to create the unified audit policy again.

   <pre>
	 CREATE AUDIT POLICY dv_realm_hr
     ACTIONS SELECT, UPDATE, DELETE
     ACTIONS COMPONENT=DV Realm Violation ON "Oracle Database Vault";
   </pre>

   This time, you are able to create the policy because user <code>HR</code> has been properly authorized in Oracle Database Vault.


4. User <code>HR</code> does not really need this policy, so drop the policy. 	 	 

   <pre>
     DROP AUDIT POLICY dv_realm_hr;
   </pre>

## Task 6: Revoke the Unified Audit Authorization from User HR   

1. Connect as the <code>DV_OWNER</code> user.

   <pre>
	 CONNECT c##sec_admin_owen@<i>pdb_name</i>
	 Enter password: <i>password</i>
   </pre>

2. Revoke the unified audit authorization from the <code>HR</code> user.

   <pre>
     EXEC DBMS_MACADM.UNAUTHORIZE_AUDIT_ADMIN ('HR');
   </pre>

	 Now, user <code>HR</code> can no longer create and manage unified audit policies in Oracle Database Vault.

## Task 7: Revoke the AUDIT_ADMI Role from HR

1. Connect as user <code>SYS</code> with the <code>SYSDBA</code>.

   <pre>
     CONNECT SYS@<i>pdb_name</i> AS SYSDBA
     Enter password: <i>password</i>
   </pre>

2. Revoke the <code>AUDIT_ADMIN</code> role from <code>HR</code>.  

   <pre>
     REVOKE AUDIT_ADMIN FROM HR;
   </pre>

## Learn More

*REVIEWERS: The following link goes to the 23c internal draft of DVADM but will be replaced with the public library link once 23c is released.*

* [Using Oracle Database Auditing with Oracle Database Vault](http://st-doc.us.oracle.com/id_common/review/docbuilder/html/F46691_01/dba-operations-in-an-oracle-database-vault-environment.htm#GUID-389D36D3-32F8-4451-965E-76448CDBBB43)

## Acknowledgements
* **Author** - Patricia Huey, Consulting User Assistance Developer, User Assistance Development
* **Last Updated By/Date** - Patricia Huey, User Assistance Development, July 2022
