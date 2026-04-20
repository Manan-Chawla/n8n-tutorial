# Automated Team Pulse Dashboard

The Team Pulse Dashboard is a streamlined, automated internal communication tool designed
to bridge the gap between daily data collection and team visibility. By integrating Baserow, n8n,
and custom web presentation layers, this project transforms raw status updates into a
professional, high-performance web dashboard.

---

## What is this Project?

This project is an end-to-end automation pipeline. It functions by periodically fetching employee
status updates from a Baserow database, processing that data through a JavaScript-driven
logic engine in n8n, and dynamically rendering a modern, "SaaS-style" HTML dashboard. It
moves away from static spreadsheets and manual reporting, offering a live "Pulse" of the team's
current activities.

---

## The Problem We Are Solving

In many professional environments, keeping track of daily progress involves tedious manual
processes:

- `Information Silos`: Data trapped in databases or spreadsheets that team members rarely check.
- `Communication Overhead`: The "Daily Stand-up" can become repetitive or difficult to track across different time zones.
- `Presentation Friction`: Raw data is often difficult to read or lacks the visual hierarchy needed to quickly identify key updates.
- `Manual Reporting`: Managers spending time compiling data into reports rather than acting on the information.

---

## How It Can Be Helpful?

The Team Pulse Dashboard serves as a centralized "Source of Truth." It is helpful in the
following ways:

- `Instant Visibility`: Any stakeholder with the webhook URL can see exactly what the team is working on without needing access to the backend database.
- `Time Savings`: Automating the fetch-and-format cycle saves hours of manual documentation every week.
- `Asynchronous Alignment`: It allows teams to stay aligned without requiring a synchronous meeting, as the dashboard is always up-to-date with the latest submissions.

---

## Steps to Create This Workflow

---

## Part 1 : The Main Workflow

### 1. Webhook Node

- Create a webhook node by clicking on `+` and then select webhook
- Configure node:
  - HTTP Method : `POST`
  - Path : `daily-standup`
  - Authentication : `None`
  - Respond : `When last node responds`
  - Response Data : `All entries`
- To test this node, copy the test url and write down the following command in cmd command prompt:

```bash
curl -X POST http://localhost:5678/webhook-test/daily-standup -H "Content-Type: application/json" -d "{\"name\":\"Bella\",\"update\":\"Testing the new automation\"}"
```

---

### 2. Create Data Table

- Go to Data Tables in n8n
- Create a new data table `daily-standup data`
- Configure table with these columns:
  - `Employee_name` : string
  - `status_update` : string
  - `created_at` and `updated_at` : default

---

### 3. Add Insert Row Node to Webhook

- Go to workflow again and click `+` icon on webhook node and attach an insert a row node
- Configure node:
  - Resource : `row`
  - Operation : `insert`
  - Data Table : `daily-standup data`
  - Values to insert:
    - `Employee_name` : `{{$json.body.name}}`
    - `status_update` : `{{$json.body.update}}`

---

### 4. Edit Field Node

- Click on `+` icon on insert a row node and select edit fields node
- Configure node:
  - Mode : `Manual Mapping`
  - Fields to Set:
    - `Employee_name` : `{{$json.Employee_name}}`
    - `status_update` : `{{$json.status_update}}`
    - `createdAt` : `{{$json.createdAt}}`

---

### 5. Setting Up BaseRow

> BaseRow is an online service which acts like a database just like MongoDB, but the best part is that it's easy to setup and useful for projects like n8n workflows.

- Go to website: `www.baserow.io`
- Create an account
- Click on Databases and create a new one, name it: `Standups`
- Copy the database id — for example, given the URL `https://baserow.io/database/420904/table/937891/1839911`, the table id will be `937891`
- Create three columns and name them as they are passed in n8n:
  - `Name` : string
  - `Update_Status` : string
  - `Date` : string
- **Most important thing is that these names have to be the same in n8n too. If they are different then it won't receive data from n8n.**
- Copy the url of this database from Chrome

---

### 6. Setting HTTP Node

- Click on `+` node, and choose HTTP request node
- Configure node:
  - Method : `POST`
  - URL : `https://api.baserow.io/api/database/rows/table/937891/?user_field_names=true`
  - Authentication : `Generic Credential Type`
  - Generic Auth Type : `Header Auth`
  - Header Auth : `Header Auth Account` and set this up:

    > Go to baserow.io again, and click on Settings > Database Tokens > select our database and create a new token > copy token.
    > Paste into the Credential of Header auth like this:
    > - Name : `Authorization`
    > - Value : `Token Token_paste_here`
    > - Allowed HTTP request domains : `All`
    > - Save it

  - Check send body
  - Body Content Type : `JSON`
  - Specify Body : `Using fields below`
  - Body Parameters:
    - `Name` : `{{$json.Employee_name}}`
    - `Update_Status` : `{{$json.status_update}}`
    - `Date` : `{{$json.createdAt}}`

---

## Part 2 : The Get Workflow

> In this part of the project we are setting up the GET process, in order to showcase the data fetched from Baserow.
> These nodes are separate from our previous workflow.

### 1. Schedule Trigger Node

- Click on `+` schedule trigger
- Configure node:
  - Trigger Interval : `Days`
  - Days Between Trigger : `1`
  - Trigger at Hour : `Midnight`
  - Trigger at Minute : `0`

---

### 2. HTTP Request

- Click on `+` node and select HTTP request node
- Configure node:
  - Method : `GET`
  - URL : same as post url
  - Authentication : `Generic Credential Type`
  - Generic Auth Type : `Header Auth`
  - Header Auth : `Header Auth Account` (choose same as we used in POST)
  - Send Query Parameter : click `on`
  - Specify Query Parameter : `Using Field Below`
  - Query Parameters:
    - Name : `filter__field_NameOfYourDateColumn__date_equal`
    - Value : `{{$today}}`

---

### 3. Add JS Code Node

- Click on `+`, and select code > JS code
- Configure node:
  - Mode : `Run Once for all items`
  - JS Code:

```javascript
const results = $input.item.json.results || [];

return results.map(item => {
    return {
        name: item.Name || "Unknown",
        update: item.Update_Status || "No update provided",
        time: item.Date ? new Date(item.Date).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : "N/A",
        initial: (item.Name || "U").charAt(0).toUpperCase()
    };
});
```

---

### 4. Aggregate Node

- As our JS code is returning items as various lists, we have to convert them into one
- Click on `+` and select Aggregate
- Configure node:
  - Aggregate : `All Item Data (into a single list)`
  - Put Output in Field : `data`
  - Include : `All Fields`

---

### 5. HTML Node

- Now we are going to create an HTML page to showcase output
- Click `+` and select HTML > Generate A HTML
- Write down the following code in it:

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Team Pulse</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f8fafc;
      padding: 40px;
      margin: 0;
    }
    .container {
      max-width: 650px;
      margin: auto;
    }
    .update-card {
      background: #fff;
      padding: 20px;
      border-radius: 14px;
      margin-bottom: 15px;
      border: 1px solid #e2e8f0;
      display: flex;
      gap: 15px;
    }
    .avatar {
      width: 48px;
      height: 48px;
      border-radius: 12px;
      background: #6366f1;
      color: #fff;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: bold;
    }
    .user-name {
      font-size: 18px;
      font-weight: bold;
    }
    .update-text {
      margin: 6px 0;
      color: #475569;
    }
    .time-stamp {
      font-size: 12px;
      color: #94a3b8;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Team Pulse</h1>

    {{ $json.data.map(item => '<div class="update-card"><div class="avatar">' + item.initial + '</div><div><div class="user-name">' + item.name + '</div><div class="update-text">' + item.update + '</div><div class="time-stamp">🕒 ' + item.time + '</div></div></div>').join('') }}

  </div>
</body>
</html>
```
