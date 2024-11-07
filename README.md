# sqlinjection
Exploiting SQL Injection vulnerability

# AIM:
To exploit SQL Injection vulnerability using Multidae web application in Metasploitable2

## DESIGN STEPS:

### Step 1:

Install kali linux either in partition or virtual box or in live mode


### Step 2:

Investigate on the various categories of tools as follows:

### Step 3:

Open terminal and try execute some kali linux commands

## EXECUTION STEPS AND ITS OUTPUT:
SQL Injection is a sort of infusion assault that makes it conceivable to execute malicious SQL statements. These statements control a database server behind a web application. Assailants can utilize SQL Injection vulnerabilities to sidestep application safety efforts. They can circumvent authentication and authorization of a page or web application and recover the content of the whole SQL database. Identify IP address using ifconfig in Metasploitable2

![image](https://github.com/user-attachments/assets/1e9a209f-8b59-44f4-92c6-52fc325808d7)

Use the above ip address to access the apache webserver of Metasploitable2 from kali linux. In Kali Linux use the ip address in a web browser.

![image](https://github.com/user-attachments/assets/a0a38d8b-04eb-4c57-a635-feb530843d3b)

Select Multidae from the menu listed as shown above. You will get the page as displayed below:

![image](https://github.com/user-attachments/assets/27ea906b-4a73-474f-8165-9463872a4824)

Click on the menu Login/Register and register for an account

![image](https://github.com/user-attachments/assets/50d30fc2-3178-4f84-a54a-76bd33a821ea)

Click on “Create Account” to display the following page: 

![image](https://github.com/user-attachments/assets/09f8cf47-d973-4b4e-855f-6071e0deee0e)

The login structure we will use in our examples is straightforward. It contains two input fields (username and password), which are both vulnerable. The back-end content creates a query to approve the username and secret key given by the client. Here is an outline of the page rationale:

($query = “SELECT * FROM users WHERE username=’$_POST[username]’ AND password=’$_POST[password]’“;). For the username put “ganesh” or “anything” and for the password put (anything’ or ‘1’=’1) or (admin’ or ‘1’=’1) then try to log in, and you’ll be presented with an admin login page.
Click “Login”. The logged in page will show as below: 

![image](https://github.com/user-attachments/assets/227a022a-fa76-4516-8b00-abd4f294b74a)

##Bypassing login field

The username field is vulnerable. Put (ganesh’ #) or (ganesh’--) in the username field and hit “Enter” to log in. We use “#” or “--” to comment everything in the query sentence that comes after the username filed telling the database to disregard the password field: (SELECT * FROM users WHERE username=’admin’ # AND password=’ ‘). By using line commenting, the aggressor eliminates a part of the login condition and gains access. This technique will make the “WHERE” clause true only for one user; in this case, it is “ganesh.”

Now after logging out you will see the login page. In the login page give ganesh’ # . You can see the page now enters into the administrator page as before when giving the password.

Click the login button and you will see it enter into the administrator page.

![image](https://github.com/user-attachments/assets/6ef3412f-8e25-4806-a240-a34e416179ae)

Union-based SQL injection
UNION-based SQL injection assaults enable the analyzer to extract data from the database effectively. Since the “UNION” operator must be utilized if the two inquiries have precisely the same structure, the attacker must craft a “SELECT” statement like the first inquiry. we will be using the “User Info” page from Mutillidae to perform a Union-Based SQL injection attack. Go to “OWASP Top 10/A1 — Injection/SQLi — Extract-Data/User Info”

After logging out, Now choose the menu as shown below:

![image](https://github.com/user-attachments/assets/5dd050cd-6c1c-4cf2-8a73-d9001d2049c5)

![image](https://github.com/user-attachments/assets/edd191c4-9e1d-452d-8868-289d3676354c)

![image](https://github.com/user-attachments/assets/b1a0b067-33d4-43a4-9684-651027d6a983)

![image](https://github.com/user-attachments/assets/10995395-9a1b-42b0-a57e-c54370d01d3e)

From this point, all our attack vectors will be performed in the URL section of the page using the Union-Based technique.There are two different ways to discover how many columns are selected by the original query. The first is to infuse an “ORDER BY” statement indicating a column number. Given the column number specified is higher than the number of columns in the “SELECT” statement, an error will be returned.


![image](https://github.com/user-attachments/assets/504e38a5-2cce-451b-8f1b-daf29e5d0761)

Since we do not know the number of columns, we start at 1. To find the exact amount of columns, the number is incremented until an error related to the “ORDER BY” clause is returned. In this example, we incremented it to 6 and received an error message, so it means that the number of columns is lower than 6.

The browser url of this info page need to be modified with the url as below:

![image](https://github.com/user-attachments/assets/cf525589-e9b5-46d8-b756-7d96893fe8fd)

After adding the order by 6 into the existing url , the following error statement will be obtained:

![image](https://github.com/user-attachments/assets/ab8ca6f4-c096-44cc-a9bb-f860b67abe62)

When we ordered by 5, it worked and displayed some information. It means there are five columns that we can work with. Following screenshot shows that the url modified to have statement added with ordered by 5 replacing 6.


![image](https://github.com/user-attachments/assets/01aa1f92-b090-44ec-a0a4-c7e585c8c4b5)

As it is having 5 columns the query worked fine and it provides the correct result


![image](https://github.com/user-attachments/assets/70d15f26-a1d2-472a-814f-ca6913359227)

Instead of using the "order by" option, let’s use the "union select" option and provide all five columns. Ex: (union select 1,2,3,4,5)

![image](https://github.com/user-attachments/assets/1310c7e8-47f3-4339-b96e-e46b7c5ab368)

As given in the screenshot below columns 2,3,4 are usable in which we can substitute any sql commands to extract necessary information.

![image](https://github.com/user-attachments/assets/3914869b-7206-4b50-9d51-a4155dbac24c)

Now we will substitute some few commands like database(), user(), version() to obtain the information regarding the database name, username and version of the database.

![image](https://github.com/user-attachments/assets/529007ba-b116-4146-88b1-9c87a8acaba1)

The url when executed, we obtain the necessary information about the database name owasp10, username as root@localhost and version as 5.0.51a-3ubuntu5. In MySQL, the table “information_schema.tables” contains all the metadata identified with table items. Below is listed the most useful information on this table.

Replace the query in the url with the following one: union select 1,table_name,null,null,5 from information_schema.tables where table_schema = ‘owasp10’

![image](https://github.com/user-attachments/assets/53e555f3-054b-4747-8350-08a5d692b5e1)

The url once executed will retrieve table names from the “owasp 10” database. ##Extracting sensitive data such as passwords

When the attacker knows table names, he needs to discover what the column names are to extract data.

In MySQL, the table “information_schema.columns” gives data about columns in tables. One of the most useful columns to extract is called “column_name.”

Ex: (union select 1,colunm_name,null,null,5 from information_schema.columns where table_name = ‘accounts’).

Here we are trying to extract column names from the “accounts” table.


![image](https://github.com/user-attachments/assets/fca6a2a2-bbe3-4986-9149-4ae8e7986bb5)

The column names of the accounts is displayed below for the following url:

![image](https://github.com/user-attachments/assets/e12bd143-6e32-4a0b-b587-905ef8a7fb57)

Once we discovered all available column names, we can extract information from them by just adding those column names in our query sentence.

Ex: (union select 1,username,password,is_admin,5 from accounts).


![image](https://github.com/user-attachments/assets/d31ebff1-d4e8-4c79-ab6e-574d98b5c4b2)

Reading and writing files on the web-server
We can use the “LOAD_FILE()” operator to peruse the contents of any file contained within the web-server. We will typically check for the “/etc/password” file to see if we get lucky and scoop usernames and passwords to possible use in brute force attacks later.

Ex: (union select null,load_file(‘/etc/passwd’),null,null,null).


![image](https://github.com/user-attachments/assets/4e90b56e-2e23-4a76-b23e-d3b948b0991c)

the “INTO_OUTFILE()” operator for all that they offer and attempt to root the objective server by transferring a shell-code through SQL infusion. we will write a “Hello World!” sentence and output it in the “/tmp/” directory as a “hello.txt” file. This “Hello World!” sentence can be substituted with any PHP shell-code that you want to execute in the target server. Ex: (union select null,’Hello World!’,null,null,null into outfile ‘/tmp/hello.txt’).

## RESULT:
The SQL Injection vulnerability is successfully exploited using the Multidae web application in Metasploitable2.
