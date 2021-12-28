# Create an HTML Table in Zoho Creator Using Deluge and Notes

In a Zoho Creator application, an HTML table is a helpful way to show external users a list, such as appointments, inventory, or contacts related to a main parent record. Clickable links and buttons can be added in the table rows to add navigational functionality, such as opening an email client, or navigating to another form to edit contact information.


<img src="html_table.png" width="400">
<p><em>Contact infomation in the snippet was randomly generated and is NOT real contact information</em></p>

## How it Works
Adding a table to a Creator form is done through a Note field. In a workflow, a variable is created to contain the HTML and CSS syntax and will be appended to throughout. A list of records are appended to the variable as rows in the table using a for each loop. Once the rows are added and HTML tags are closed, the Note field will then be set to equal the variable. 

In order to use HTML in Deluge, the HTML and CSS syntax must be enclosed with quotation marks. 


## Set Up
Prior knowledge of HTML, CSS, and Deluge will be helpful. For this example, we will show a list of all related contacts for an Account Record.
You will need to set up the following:

* A Zoho Creator form with:
  * a note field
  * an Account ID field
* CRM oauth connection in Creator 

Next, set up a workflow on User Input of the Account ID field. Let's begin with an if statement on the condition that the Account ID is not null.


```
if(input.ACCOUNT_ID != null)
{

}

```

Inside of this if statement, let's begin adding the table styling in an HTML head tag and store this in a variable called 'x'. Notice how all of the HTML and CSS syntax are contained in quotations:

```
if(input.ACCOUNT_ID != null)
{
	//style the table
	x = "<head><style> td { text-align: center;  padding: 12px 15px; }th {background: #313949; color:#CAC7C3; text-align:center; padding: 7px 5px;}button{color:white; padding: 8px 7px; border-radius: 5px}.edit{background-color: #313949;}.delete{background-color:red }table{width: 100%; margin: 0 auto; box-shadow: 0 0 20px rgba(0, 0, 0, 0.15); border-collapse: collapse; background-color: white;} tbody tr:nth-of-type(even){background-color:#f3f3f3;} tbody tr{border-bottom: 1px solid #dddddd;} tbody tr.active-row{font-weight: bold;color: #009879;}.edit a{color:white}</style></head>";
	...

```

Note: Inline styling can be used.

Append your table and table header tags to the 'x' variable. I would like my table headers to read "Name", "Phone", and "Email". 


```
...
	//start the table and add the headers
	x = x + "<table><thead><tr><th>Name</th><th>Phone</th><th>Email</th> </tr> </thead><tbody>";
...	

```

Get the related list that you want to use and store this into a list variable, then iterate through the list with a for each loop, getting the pieces of information you need for each contact. In this example, I want to get the first and last name, email and phone number. I have omitted the null and empty checks for brevety, but you may want to add these in case you do not have complete data coming from your CRM.

```
...
	//iterate through all contacts for a particular account record
	getRelatedContacts = zoho.crm.getRelatedRecords("Contacts","Accounts",input.Account_ID);
	for each contact in getRelatedContacts
	{
		getRecord = zoho.crm.getRecordById("Contacts",contact.get("id").toLong());
		getFirstName = getRec.get("First_Name");
		getLastName = getRec.get("Last_Name");
		phone = getRec.get("Phone");
		email = getRec.get("Email");
		//
		//adds the current iteration's contact name, phone and email to the table row. Append the x variable so it's storing the contact info each time. Clicking the email will open your email client. 
		//
		x = x + "<tr><td>" + getFirstName + " " + getLastName + "</a></td><td>" + phone + "</td><td><a href='mailto:" + email + "'>" + email + "</a></td></tr>";
	}
...

```
Append the closing table tags to the 'x' variable, then set the Note field to equal 'x'.

```
...
//close the table tags
	x = x + "</tbody></table>";
//set the blank note to equal x
	input.YOUR_NOTE_VARIABLE= x;
}
//end of the if statement if Account_ID == null
```
