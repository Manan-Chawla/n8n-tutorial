# **Automated Team Pulse Dashboard**
The Team Pulse Dashboard is a streamlined, automated internal communication tool designed
to bridge the gap between daily data collection and team visibility. By integrating Baserow, n8n,
and custom web presentation layers, this project transforms raw status updates into a
professional, high-performance web dashboard.


## **What is this Project?**
This project is an end-to-end automation pipeline. It functions by periodically fetching employee
status updates from a Baserow database, processing that data through a JavaScript-driven
logic engine in n8n, and dynamically rendering a modern, "SaaS-style" HTML dashboard. It
moves away from static spreadsheets and manual reporting, offering a live "Pulse" of the team’s
current activities.


---


## **The Problem We Are Solving**
In many professional environments, keeping track of daily progress involves tedious manual
processes:
● ```Information Silos```: Data trapped in databases or spreadsheets that team members rarely
check.
● ```Communication Overhead```: The "Daily Stand-up" can become repetitive or difficult to
track across different time zones.
● ```Presentation Friction```: Raw data is often difficult to read or lacks the visual hierarchy
needed to quickly identify key updates.
● ```Manual Reporting```: Managers spending time compiling data into reports rather than
acting on the information.

------

## **How It Can Be Helpful?**
The Team Pulse Dashboard serves as a centralized "Source of Truth." It is helpful in the
following ways:
● ```Instant Visibility```: Any stakeholder with the webhook URL can see exactly what the team
is working on without needing access to the backend database.
● ```Time Savings```: Automating the fetch-and-format cycle saves hours of manual
documentation every week.
● ```Asynchronous Alignment```: It allows teams to stay aligned without requiring a
synchronous meeting, as the dashboard is always up-to-date with the latest submissions.


-----------------


## **Steps to Create This Workflow**

## **Part 1 : The Main Workflow**

1.**Webhook Node**
- Create a webhook node by click on '+' and then select webhook
- congifure node :-
HTTP Method : POST
Path : daily-standup
Authentication : None 
Respond : When last node responds
Responde Data : All entries
- To test this node, copy the test url and write down the following command in cmd command prompt
```
curl -X POST http://localhost:5678/webhook-test/daily-standup -H "Content-Type: application/json" -d "{\"name\":\"Bella\",\"update\":\"Testing the new automation\"}"
```

2.**Create Data Table**
- got to Data Tables in n8n
- create a new data table 'daily-standup data'
- configure table by these columns
Employee_name : string
status_update : string
created_at and updated_at : default


3.**Add insert row node to webhook**
- go to workflow again and click '+' icon on webhook node and attach a insert a raw node
- configure node :-
Resource : row
Operation : insert
Data Table : daily-standup data
Values to insert : Employee_name : {{$json.body.name}} | status_update : {{$json.body.update}}


4.**Edit Field Node**
- click on '+' icon on insert a row node and select edit fields node
- configure node :-
Mode : Manual Mapping
Fields to Set : Employee_name : {{$json.Employee_name}} | status_update : {{$json.status_update}} | createdAt : {{$json.createdAt}}

5.**Setting Up BaseRow**
```
BaseRow is online service which acts like a database just like mongodb, but the best part is that it's easy to setup and useful for project like n8n workdflow
```
- go to website : ``` www.baserow.io ```
- Create account
- Click on Databases and create a newone named it : ```Standups```
- Copy the database id : https://baserow.io/database/420904/table/937891/1839911, then the database id will be ```937891```
- For instance like if this is url 
- create three columns name them as the are passing in n8n
Name : string
Update_Status : string
Date : string
- ```Most important thing is that these name has to be same in n8n too, if they are different then it wont receive data from n8n```
- copy the url of this database from chrome


6.**Setting HTTP Node**
- click on '+' node, and choose HTTP request node
- configure node :-
Method : POST
URL : https://api.baserow.io/api/database/rows/table/937891/?user_field_names=true
Authentication : Generic Credential Type
Generic Auth Type : Header Auth 
Header Auth : Header Auth Account and setup this 
```
Go to baserow.io again, and click on setting > Database Tokens > select our database and create new token > copy token
Pase into the Credential of Header auth like this
Name : Authorization
Value : Token Token_paste_here
Allowed HTTP request domains : All
save it
```
Check send body 
Body Content Type : JSON 
Specify Body : Using fields below
Body Parameters :
Name : {{$json.Employee_name}}
Update_Status : {{$json.status_update}}
Date : {{$json.createdAt}}
