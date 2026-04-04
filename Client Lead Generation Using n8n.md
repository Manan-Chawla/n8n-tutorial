# **Lead Researcher Automation Using n8n**
--- 
In the real world, businesses spend hours researching new leads or clients before a meeting. This workflow automates that entire "homework" phase. Instead of manually searching Google News, checking a CRM, and drafting an email, you just type a name into a form, and the automation handles the research, database logging, and AI drafting in seconds.
Real-Life Use Cases:
1. **Agency Owners**: When a potential client fills out a contact form, the AI immediately finds recent news about their company and drafts a personalized "Icebreaker" email for you to review.
2. **PR Professionals**: To track a specific brand and automatically generate a summary of their daily media mentions.
3. **Sales Teams**: To filter high-value leads into HubSpot while keeping a simple backup of smaller leads in a local data table.
---



## **Workflow's flowchart**
<img width="2487" height="1602" alt="Lead Researcher Automation Using n8n - visual selection" src="https://github.com/user-attachments/assets/61419592-ba31-45a0-b002-ee18fe7e5d27" />


## **Steps to Create this workflow**

### **Node 1 : n8n on form submission form**

1. click on '+' button
2. select 'on form submission'
3. configure node:-
   1. authentication : none
   2. form title : Automation Building Request Form
   3. form description : Enter details below in order to get a automation tool in just 5 days.
   4. Add following form elements :-
      1. first name | last name :- text input type
      2. email :- email type
      3. message :- textarea type
      4. what is your monthly revenue in INR? :- radio button type | option :- [less than 25k, between 35k to 50k, 1 lakh, 10 lakh]
      5. What is your budget ? :- number type
      6. project :- text input type
   5. Respond when : form is submitted
4. Now just execute this in order to run this node.

---

### **Node 2 : Creating data table for storing data**

1. Go to personal
2. click on data table
3. create new table named it 'Lead generation data'
4. add following columns in it:-
   1. first_name : text
   2. last_name : text
   3. email : text
   4. message : text
   5. budget : number
   6. project : text
5. Go back to workflow and add insert row node to last form node
6. configure 'insert row node' :-
   1. resource : row
   2. operation : insert
   3. Data table : Lead generation data
   4. Mapping Column node : Map each column manually
   5. Values to insert : make sure you drag and drop value as per column
7. After adding columns by drag drop, just execute step in order to recive data.



### **Node 3 : Filter Node**

1. click on '+' button of last node and search for 'filter' and select it
2. configure node:-
   1. As we are creating a budget for project so,
      key = {{$json.budget}} | # is greater than | value = 5000
3. Execute this, as if the budget is low then user will not be able to receive any update.


### **Node 4 : Gemini Message A Model Node**

1. click on '+', and search for gemini and then select 'message a model'
2. For this get a api key from 'aistudio.google.com'
3. configure node :-
   1. Insert API key into credential and save it
   2. Resource : Text
   3. Operation : Message a model
   4. Model : models/gemini-2.5-flash
   5. Prompt :-
      ```
      Act as a professional corporate lead generation strategist. 
      Generate a professional message for:
      Name: {{ $json.First_Name }}
      Email: {{ $json.Email }}
      Budget: {{ $json.Budget }}
      Project: {{ $json.Project }}
      Goal: Express excitement about building an automation tool for their project within their budget.Remember the company which sending these mail is ByteEdu Tech.
      IMPORTANT: Provide ONLY the message text itself. Do not include any introductory remarks, conversational filler, or explanations (e.g., "Here is the message...").
      Start directly with the subject line or the greeting.
      ```
   6. Role : user
   7. Tick 'Simplify output'
   8. Execute step


### **Node 5 : HTML Node**
1. click on '+' node and search fot html and select generate html template
2. configure node :-
   1. Opeartion : Generate HTML Template
   2. Code :
      ```
      <!DOCTYPE html>
      <html>
      <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link rel="preconnect" href="https://fonts.googleapis.com">
        <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
        <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500&family=Poppins:wght@600;700&display=swap" rel="stylesheet">
  
      <style>
       :root {
      --primary: #2563eb;
      --bg-gradient: linear-gradient(135deg, #f8fafc 0%, #e2e8f0 100%);
      --text-main: #1e293b;
      --text-muted: #64748b;
       }

      body {
       margin: 0;
       padding: 40px 20px;
       background: var(--bg-gradient);
       font-family: 'Inter', sans-serif;
       color: var(--text-main);
       line-height: 1.6;
       } 

      .card {
       max-width: 650px;
       margin: 0 auto;
       background: #ffffff;
       border-radius: 24px;
       overflow: hidden;
       box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.05), 0 10px 10px -5px rgba(0, 0, 0, 0.02);
       border: 1px solid rgba(255, 255, 255, 0.7);
       }

      .header-accent {
       height: 8px;
       background: linear-gradient(90deg, #2563eb, #7c3aed);
      }

      .content-area {
       padding: 50px 60px;
       } 

      .brand-logo {
       font-family: 'Poppins', sans-serif;
       font-weight: 700;
       font-size: 22px;
       letter-spacing: -0.5px;
       color: var(--primary);
       margin-bottom: 30px;
       display: flex;
       align-items: center;
       }

      .brand-logo::before {
       content: '';
       display: inline-block;
       width: 12px;
       height: 12px;
       background: var(--primary);
       border-radius: 3px;
       margin-right: 10px;
      } 

      .main-text {
       font-size: 16px;
       color: #334155;
       /* Preserves Gemini's line breaks and spaces */
       white-space: pre-wrap; 
      }

      .cta-container {
       margin-top: 40px;
       padding-top: 30px;
       border-top: 1px solid #f1f5f9;
       }

      .btn {
       display: inline-block;
       padding: 14px 28px;
       background: var(--primary);
       color: #ffffff !important;
       text-decoration: none;
       border-radius: 12px;
       font-weight: 500;
       font-size: 15px;
       transition: transform 0.2s ease;
       box-shadow: 0 4px 6px -1px rgba(37, 99, 235, 0.2);
       }

      .footer {
       text-align: center;
       margin-top: 30px;
       font-size: 13px;
       color: var(--text-muted);
       }

      /* Mobile adjustments */
      @media (max-width: 600px) {
       .content-area { padding: 30px; }
       body { padding: 20px 10px; }
      }
      </style>
      </head>
      <body>
       <div class="card">
       <div class="header-accent"></div>
       <div class="content-area">
      <div class="brand-logo">AUTOMATION ENGINE</div>
      
      <div class="main-text">
      {{ $json.content.parts[0].text }}
      </div>

      <div class="cta-container">
        <a href="#" class="btn">Confirm Project Details</a>
      </div>
      </div>
      </div>

      <div class="footer">
       &copy; 2026 Corporate Lead Generation Services &bull; Secure Portal
      </div>
      </body>
      </html>
      ```
   3. Execute step
  

### **Node 6 : Convert to file**
1. click on '+' and select conver to file
2. configure node :-
   1. Operation : Conver to text file
   2. Text Input Field : Input
   3. Put Output file in field : lead-proposal.html


