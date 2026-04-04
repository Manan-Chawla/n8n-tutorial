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
