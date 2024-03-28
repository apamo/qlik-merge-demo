# qlik-merge-demo

## Content in this repository

1. In-Merge App Demo
- In-app Merge App.qvf: Qlik app containing a load script with the *Merge Only* prefix.
- In_App_Merge_Automation.json: Qlik automation containing a workflow to insert changes into SQL Server database, trigger a partial reload of 'In-app Merge App.qvf' and notify the admin user by email.
- In_App_Merge_Script.qvs: Load script exported from app 'In-app Merge App.qvf'

2. QVD Merge Demo
- QVD Merge App.qvf: Qlik app reading from the QVD file created by the script object 'QVD Merge Script.qvf'.
- QVD Merge Script.qvf: Qlik script object to create a QVD file first and load changes with *Merge* and append to originally created QVD file.
- QVD Merge Script.qvs: Load script exported from script object 'QVD Merge Script.qvf'


## Pre-requesites

### Data Source

- SQL Server with Northwind sample database
- Add two new columns to the Customers table: 'ModifiedDate' as a datetime data type and Operation as text.
- Create a SQL trigger to store the current timestamp on 'ModifiedDate' column when a record is modified:

`CREATE TRIGGER trg_Customers_UpdateModifiedDate
ON dbo.Customers
AFTER UPDATE
AS
UPDATE dbo.Customers
SET ModifiedDate = CURRENT_TIMESTAMP
WHERE CustomerID IN (SELECT DISTINCT CustomerID FROM inserted);`

### Qlik Cloud

- A tenant in Qlik Cloud
- Enough User Permissions to upload content to the tenant (If you don't have Tenant Admin role then check with your Administrator)

## Setup

1. Import the corresponding .qvf file(s) into a Shared Space in your tenant
2. Re-create the SQL Server data connection
3. Modify the load script to use the previously created data connection
4. Reload the app (normal full reload)
5. Go to Automations and create a new one from a blank template
6. Once in the Automation Editor, right-clik and 'Upload Workspace', then select the corresponding .json file.
7. Select any 'Microsoft SQL Server' block and under 'Connection' edit the existing one with your connection parameters or create a new one.
8. (Optional) Select the 'Mail' block and under 'Connection' configure your own SMTP server e.g. host=smtp.gmail.com port=587 security=starttls and your user credentials. For Gmail, follow the steps in this guide to create an *App Password* [How to send emails from your Gmail account with SMTP with Qlik Application Automation](https://community.qlik.com/t5/Official-Support-Articles/How-to-send-emails-from-your-Gmail-account-with-SMTP-with-Qlik/ta-p/1815528)

### In-app Merge Demo

For In-app Merge execute the following steps:
- Open the app In-app Merge App.qvf
- Go to the 'Load Editor' and reload the app
- Open the corresponding 'In-app Merge' automation and setup your own workflow. Note that you can make changes to the SQL Server by using the configured blocks in the automation or with your preferred DB client e.g. DBeaver
- Make changes to the Customer table manually or with a SQL query editor if you've decided to use the automation only for triggering a partial app reload (Qlik Cloud Services blocks only)
- Run the automation
- Quickly go back to opened Qlik app in your browser (In-app Merge App.qvf) to see the changes reflected in the UI.

### QVD Merge Demo

For In-app Merge execute the following steps:
- Open the script QVD Merge Script.qvf 
- Move the 'First Load' tab between 'Main' tab and 'End Script' tab
- Reload the script to create the QVD file. Verify the QVD has been created successfully.
- Move the 'Merge Load' tab between 'Main' tab and 'End Script' tab so that 'First Load' doesn't get executed in subsequent reloads.
- Open the automation and make the necessary adjustment. Again, it's up to you to make changes to the SQL Server through the automation or not.
- Open the final app QVD Merge App.qvf
- Run the automation
- Quickly go back to opened Qlik app in your browser (QVD Merge App.qvf) to see the changes reflected in the UI.
