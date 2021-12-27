# Create an HTML Table in Zoho Creator Using Deluge and Notes

## Use Case
An HTML table can be a helpful way to show an external user with no access to the connected CRM a list of related records to a parent record. Some use cases could be a list of related appointments, inventory, or household members. Clickable links and buttons can be added in the table rows to access particular information about each record. 

How are we going to accomplishe this? do a summary of how this works, then show the steps HTML tags has to be added in Deluge with " ", and notes are the best way to show html in a creator form. Ultimately, we will be appending x with the next set of HTML code, then setting the note field to equal x. 

<img src="table.jpg">

## Set Up
Prior knowledge of HTML, CSS, and Deluge will be helpful. For this example, we will show a list of all related contacts for an Account Record. 
You will need to set up the following:

* A Zoho Creator form with:
  * a note field
  * an Account ID field
* CRM oauth connection in Creator

Next, set up a workflow on User Input of Account ID. Let's begin with an if statement on the condition that the Account ID is not null.


```
if(input.ACCOUNT_ID_VARIABLE != null)
{

}

```

Inside of this if statement, let's start adding the styling in a head tag and store this in an x variable. This could be named anything. :

```
if(input.ACCOUNT_ID_VARIABLE != null)
{
	//if you want to style the whole table at once, start with this and append the start of the table to this first x
	//
	x = "<head><style> td { text-align: center;  padding: 12px 15px; }th {background: #313949; color:#CAC7C3; text-align:center; padding: 7px 5px;}button{color:white; padding: 8px 7px; border-radius: 5px}.edit{background-color: #313949;}.delete{background-color:red }table{width: 100%; margin: 0 auto; box-shadow: 0 0 20px rgba(0, 0, 0, 0.15); border-collapse: collapse; background-color: white;} tbody tr:nth-of-type(even){background-color:#f3f3f3;} tbody tr{border-bottom: 1px solid #dddddd;} tbody tr.active-row{font-weight: bold;color: #009879;}.edit a{color:white}</style></head>";
	...

```

Note: Inline styling can be used.

Append your table and table header tags.


```
...
	//start the table and add the headers
	x = x + "<table><thead><tr><th></th><th></th><th>Name</th><th>Phone</th><th>Email</th> </tr> </thead><tbody>";
	//
...	

```

Store the related contacts into a list, then iterate through the list with a for each loop. I have omitted the null and empty checks for brevety, but you may want to add these in case you do not have complete data coming from your CRM.

```
...
//iterate through all contacts in hh
	getRelatedContacts = zoho.crm.getRelatedRecords("Contacts","Accounts",input.Account_ID);
	for each  contact in getRelatedContacts
	{
		getRec = zoho.crm.getRecordById("Contacts",contact.get("id").toLong());
		getFName = getRec.get("First_Name");
		getLName = getRec.get("Last_Name");
		ph = getRec.get("Phone");
		email = getRec.get("Email");
		//
		//adds the name, phone and email. to the table row. append the x variable so it's storing it each time. Click delete will delete record, edit will naviate to crm to make changes, email will open email client. 
		//
		x = x + "<tr><td><button class='delete'><a href='YOUR_DELETION_FORM_CONFIRMATION_URL?Account_ID=" + input.Account_ID + "&Contact_ID=" + contact.get("id") + "'>Delete</a></button></td><td><button class='edit'><a href='YOUR_EDIT_CONTACT_FORM_URL?Account_ID=" +input.Account_ID + "&Contact_ID=" + contact.get("id") + "'>Edit</a></button></td><td>" + getFName + " " + getLName + "</a></td><td>" + ph + "</td><td><a href='mailto:" + email + "'>" + email + "</a></td></tr>";
	}
...

```
Append the closing table tags and 

```
...
//close the table tags
	x = x + "</tbody></table>";
	//make the notes =  to x where we have been storing the table 
	//input.plain = x;
	//this is the blank note I created
	input.YOUR_NOTE_VARIABLE= x;
	show YOUR_NOTE_VARIABLE;
}
//end of the if statement if Account_ID == null
```
