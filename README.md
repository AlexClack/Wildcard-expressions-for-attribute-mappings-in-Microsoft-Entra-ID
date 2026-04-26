# Wildcard-expressions-for-attribute-mappings-in-Microsoft-Entra-ID
Wildcard expressions for attribute mappings in Microsoft Entra ID  

Although there is no built-in wildcard function you are able to combine the available functions to achieve a similar outcome. 
Below is the complete solution looking for multiple wildcards for those who are after a solution and can understand what is happening. For everyone else the explanation/ breakdown is further down.  

Wildcard equivalent of &ast;string&ast;  
IIF(InStr(ToLower([companyName]), "micro")>0,"1",  
IIF(InStr(ToLower([companyName]), "amazo")>0,"1",  
IIF(InStr(ToLower([companyName]), "gith")>0,"1",  
IIF(InStr(ToLower([companyName]), "insta")>0,"1",  
"0"))))  

 Wildcard equivalent of string*  
IIF(InStr(ToLower([companyName]), "micro")=1,"1",  
IIF(InStr(ToLower([companyName]), "amazo")=1,"1",  
IIF(InStr(ToLower([companyName]), "gith")=1,"1",  
IIF(InStr(ToLower([companyName]), "insta")=1,"1",  
"0"))))  

Functions in use:  
IIF - The IIF function returns one of a set of possible values based on a specified condition.  
https://learn.microsoft.com/en-us/entra/identity/app-provisioning/functions-for-customizing-application-data#iif  
InStr - The InStr function finds the first occurrence of a substring in a string.  
https://learn.microsoft.com/en-us/entra/identity/app-provisioning/functions-for-customizing-application-data#instr  
ToLower - Takes a source string value and converts it to lower case using the culture rules that are specified. If there's no culture info specified, then it uses Invariant culture.  
https://learn.microsoft.com/en-us/entra/identity/app-provisioning/functions-for-customizing-application-data#tolower  


Explanation:  
When attempting to search with a wildcard we are looking for a specific string in the source (in the examples above companyname is the source attribute)  
We don’t have that functionality but we can generate a similar outcome by combining functions.  
Assume that the companyName attribute is set to “microsoft” and the wildcard we want to search for is “micro”  
The first issue is finding the string to do this we can use the InStr function to search the companyName for “micro”:  
InStr([companyName]), "micro")  

This returns the value of “1” since that is the first occurrence of the string.  
If the companyName was “trendmicro” we would receive the value of “6” since that is the first occurrence of our string.  
If the companyName was “bobsbuilders” we would receive the value of “0” since there is no occurrence of our string.  
InStr is case sensitive so the companyName “Microsoft” would return a “0” value. To make it insensitive we use the ToLower function to change the value of companyName we are assessing to lower case and all of our search strings will be in lower case.  
InStr(ToLower([companyName]), "micro")  

Now the companyName “Microsoft” will return a value of “1” because the value that is being assessed in the InStr function is “microsoft”.  
We are currently receiving the value of “1” when the companyName is “Microsoft” and generate the value of “6” when the companyName is “TrendMicro”. There are 2 wildcards we can attempt.  

String*  
&ast;String&ast;  

For string*  
We use the IIF function which gives a True or False output – but you can set the output for True and False. In this example we say if the value of the InStr function equals “1” (string detected at the start) then the value of the IIF function True is “1”. The value of False is “0”  
IIF(InStr(ToLower([companyName]), "micro")=1,"1","0")

For &ast;string&ast;  
We use the IIF function again but instead of looking for the value to equal “1” we look for it to be greater than “0” (String not detected) 
IIF(InStr(ToLower([companyName]), "micro")>0,"1","0")


Additional use:  
With this we’ve identified the string we were searching for and generated a positive and negative output. You can change the output to whatever you need e.g. the original companyName etc  
IIF(InStr(ToLower([companyName]), "micro")>0,[companyName],"Not Found")

You can also do this with any attribute you are importing e.g. department  
IIF(InStr(ToLower([department]), "sale")>0,[department],"Not Found")

You can create an expression that searches for multiple wildcards by nesting the IIF statements as below:  

Wildcard equivalent of &ast;string&ast;  
IIF(InStr(ToLower([companyName]), "micro")>0,"1",  
IIF(InStr(ToLower([companyName]), "amazo")>0,"1",  
IIF(InStr(ToLower([companyName]), "gith")>0,"1",  
IIF(InStr(ToLower([companyName]), "insta")>0,"1",  
"0"))))  

 Wildcard equivalent of string*  
IIF(InStr(ToLower([companyName]), "micro")=1,"1",  
IIF(InStr(ToLower([companyName]), "amazo")=1,"1",  
IIF(InStr(ToLower([companyName]), "gith")=1,"1",  
IIF(InStr(ToLower([companyName]), "insta")=1,"1",  
"0"))))  

To add a new line copy one line of the IIF statement and add a bracket on the last line e.g. 
IIF(InStr(ToLower([companyName]), "micro")=1,"1",  
IIF(InStr(ToLower([companyName]), "amazo")=1,"1",  
IIF(InStr(ToLower([companyName]), "gith")=1,"1",  
IIF(InStr(ToLower([companyName]), "insta")=1,"1",  
IIF(InStr(ToLower([companyName]), "mywork")=1,"1",  
"0")))))  

Keep in mind:  
The strings you search for must be lower case or the expression will always return a False value. Additionally if your expression has multiple nested IIF statement and is being used against 1000's of objects you may see a delay in provisioning times. Always test the solution.

