---
title: "Dynamic PowerBI RLS Rules"
date: 2020-11-07T10:26:20+01:00
draft: false
comment: true
categories: "Javascript"
tags: [microsoft, PowerBI, RLS, PowerToos, Security, Data, Reporting]
Javascript: false
NodeJS: false
PowerApp: true
Tech: false
Misc: false
discussionId: PowerBI-RLS-Rules-2020-10
---

Hello Observers,

as you might know I am working for a big company and I am also responsible for reporting. In this role I am developing **PowerBI** dashboards and reports for the whole organization. One specific question to be solved is data security and access rules. I just want to share some insights and a quick tutorial how to setup a simple rule set with multiple tables.

## General

In larger companies it is mandatory to think about rules and rights in terms of access management also for **PowerBI** reports. Many times a lot of data sources are combined to a new dashboard and analytics functionallity the users normally won't access. This add some major risk is terms of unauthorized data usage or industrial spying. 

Good to know that **PowerBI** has included a feature called **RLS** (Row Level Security) to manage access to certain data points.

The RLS is restricting the data what users can see on row level by matching an role expression with a user input. Normally this is pretty straight forward. But what if you not only have one attribute limiting the access of the users to the report? In general speaking you could create multiple roles for each scenario that your users need (e.g. role for US sales, one role for German sales etc.). But this would easily blow up the complete role management even in small reports. And what if a user need much more precise access to only one plant, one company, one site? An more efficient way to manage this is using just one role but limiting the access with **coding**!

## Setting up sample Data

For our example I have set up some sample data in a PowerBI file (the example file could be downloaded at the end of the tutorial). 

I create three sample tables: "RLS User", "Company" and "Sales". Each of the tables hold different columns.

![Sample Data](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image001.png)

This is of course only a simple example but the logic could be extended to way complex data models without any issue. 

You might wondering about the table called "RLS User". This table is specific created for the logic we want to build and is normally not included in your raw data model. In the simplest way this table holds a username as "RLS User" and an attribute that is allowed to be accessed (e.g. company name)

> **Remember**: If you use PowerBI with Office 365 the "username" should always the login principal name of the users of your company which is normally id@company.de

In our example we have the users "test" and "test2" and want to give them access to "Company1" and in case of "test2" only country "Germany" and for "test" only country "USA".

![Data](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image002.png)

This basic scenario is very common in reality. Imagin different sales managers should only see the turnover or sales in specific countries AND for specific sales orgs.

## Setting up the rules

But how we are limiting the users based on the RLS table we just created? For that we are using the build in **roles manager** of PowerBI Desktop.

![Access Role Manager](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image003.png)

On the Modeling tab we find the "Manage roles" tab. This is our main window to configure the access rules to our data.

![Manage roles](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image004.png)

Once open, we can click on "Create" to create a new Role and name it "RLS". After we created the role we may observe that the manager automatically pulls all tables that are currently loaded into the data model of our PowerBI file (if we load more later, they are added here).

## Writing the rule set
### Filter User Table
The first rule we insert in our data model is to filter out the "RLS User" table based on the login of the current user. Example: If we publish our Report to PowerBI Web Services and the user "test" opens the report, our rule will filter the table based on his/her userid.

To do this we are just using the following DAX (Data Analytics Expression) code:

```dax
[RLS User] = UserPrincipleName()
```
and in PowerBI it will look like:

![Manage roles](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image005.png)

This is one of the important steps as the userid cannot be manipulated while accessing the report unless the user's Office 365 password has been stolen or hacked. 


### Filter data tables
To now filter the data tables we must apply a more complex rule. This is because we want to **dynamically** filter the data from two different tables (Company AND Sales) based on the current user. To do this we use the following code:

```dax
[Company]
IN CALCULATETABLE (
    VALUES('RLS User'[Attribute]),
    FILTER(
        ALL('RLS User'),
        [RLS User] = UserPrincipalname()
    )
)
```
But what happens here? So lets check the individual parts of the code: In the first line we call the column that should be filtered in the table "Company" (remember the table holds two columns, company and employees)
```dax
[Company]
```
Now we are creating a temporary table with
```dax
CALCULATETABLE()
```
In this table we insert all **VALUES** that are found in the table "RLS User" in his column "Attribute" filtered by the actual username (so basically we apply the same rule as above to **FILTER** the table by the user logged in)
```dax
VALUES('RLS User'[Attribute]),
FILTER(ALL('RLS User'),[RLS User] = UserPrincipalname())
```
If we combine all of these we would get a temporary table with only the companies that the user is allowed to access. Aferwards the column "Company" gets filtered by exactly these entries. 

This is how it looks in PowerBI:

![Company filter](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image006.png)

>**Note**: In reality the temporary table may hold many different entries for that user. This is harmless to our logic as the filter will just ignore not matching entries.

### Extending to the second table

Basically we apply exactly the same formula to the second table in our data set. We just exchange the column name that must be filtered.

![Sales filter](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image007.png)

As you can see, we could repeat the same logic for as many tables as we need to filter out the data correctly. 

## Result

After we set out our rules we confirm and move back to the main window of PowerBI Desktop. Now lets discover our new access rules live in action!

Next to the "Manage Roles" button we find a "View as" option. This a quite nice feature as it enables us to view the report as another user and check if our rule set is working.

![View as](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image008.png)

So in the "View as" window we can now enter a specific userid (in our example the user "test") and a role ("RLS") that we previous created. 

![Select user](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image009.png)

Confirming our selection and coming back to the data we can observe that our data tables are now filtered according our rules. The "RLS User" is equal "test" and has the two attributes "Company1" and "USA". Because of our rules the tables "Company" and "Sales" are now filtered to show only the matching attribute rows. 

![Filtered data](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image010.png)

## Last Steps
Now we created a working rule set in our PowerBI file and we are ready to publish it to the cloud services. If we would do and also give users access to the report (yes, you still have to share the report with the users) they would face an empty report. But why? The answer is Microsoft. The current logic is that you are creating rule sets in your desktop version but you have to assign user to the roles in the web version before they can use it so that PowerBI knows which rule set to apply.

To do this just publish your report and login to the web portal. Go to your workspace and select the dataset you just published. 

![Security data](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image011.png)


Click on the three dots at your dataset and click on "Security" to enter the RLS section.

![Security data](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/image012.png)

Here you must add all users that should be assigned to the RLS role. You can also add distribution lists of your company so that you dont have to add or remove new users manually. To make it perfect you can then just share the report with all users as they will not access any data unless they are within this RLS role.

So we now succesfully implemented **ROW LEVEL SECURTY** in our data model.

## Conclusion

RLS is a powerful way to implement data access rules and limitations if it is necessary due to company policy or compliance reasons. The logic in this tutorial could be extended to way more tables. But please remember that you have to maintain the "RLS User" table with entries for all filters you set in the role manager. Otherwise the user will not be able to see any data. Especially if you go one step further and create relations between the tables so you face cross-filtering. 

You can download the sample file [here](https://s3.eu-central-1.wasabisys.com/gwce.public/blog/PowerBI-RLS-Rules-2020-10/CO-example.pbix).
