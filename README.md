# Advanced Web Server Log Analysis for Security and Transaction Monitoring with Splunk
In this project, I took on the role of a data analyst tasked with providing valuable insights to various users. My responsibilities included gathering and reporting data related to purchases made at the company, with a focus on identifying suspicious activities, particularly around failed transactions and credit card data. Through thorough analysis, I aimed to tell meaningful stories that could help users detect potential security issues and vulnerabilities within the organization.

Project Files:
WebServer_access.log,

Apache Web Server Codes.csv


## Web Server Access Log Analysis Using Splunk


In this project, I was provided with a sample access log file for a web server (WebServer_access.log) to demonstrate how Splunk can be used to analyze company data. I uploaded the data and enriched it by creating a lookup to add a description for each status code in the log.

### **Steps I followed:**


Navigated to Settings > Add Data.
Selected the Upload method to add the sample log file.
Dragged the log file from my desktop into the “Drop your data file here” area or clicked Select to choose the file.
Once the file was uploaded, I clicked Next.
Splunk automatically detected the source type as access_combined_wcookie.
I clicked Next.
Set the host value to csvd and selected the main index (the default index).
Clicked Review to verify the details of the uploaded data.
Clicked Submit to finalize the process.
To view the uploaded events, I selected Start Searching. Alternatively, I used the following search string in the search and reporting app to find the data:
```spl
source="csvd"
```
Number of events stored in the sample access log: 12,992



----------------------------------------------------------------------------------------------------------------------



## **Creating a CSV Lookup for HTTP Status Code Descriptions in Splunk**


In this project, I created a CSV lookup using the serverCodes.csv file to map status codes to their corresponding descriptions for analyzing errors in the access log. The most prevalent status code description in the uploaded access log was HTTP_SERVICE_UNAVAILABLE.

### **Steps I followed:**

Navigated to Settings > Lookups in the Splunk bar.
Clicked Add New next to "Lookup table files".
Chose the search app as the destination app and uploaded the lookup file Apache Web Server Codes.csv. I set the destination file name as serverCodes.csv and clicked Save.
Returned to the Lookups tab and clicked Add New next to "Lookup definition".
Named the lookup definition as Server Code Lookup and selected the previously defined lookup table file serverCodes.csv.
Set the destination app to search and named the lookup serverCodes.csv, then associated it with the defined lookup table file.
Clicked Save.
Verifying the Lookup:
To ensure the lookup was working properly, I ran a search and piped the results into the lookup command as follows:
```spl
"lookup serverCodes.csv status AS status OUTPUT code_description AS code_description"
```
I then selected the code_description field under "Selected Fields" to view the mapped values and analyzed their prevalence.

Most prevalent code description: HTTP_SERVICE_UNAVAILABLE
![Screenshot (535)](https://github.com/user-attachments/assets/e8f58559-2824-4883-95cc-eb2eff559886)

------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Create a Map Visualization

I aimed to visualize the geographical locations of IP addresses associated with declined transactions, producing a map by country.

Fields of Interest
```
source
sourcetype
host
msg
clientip
Country
lat
lon
```
Splunk Commands Used
I used the following commands:

```iplocation```: Retrieves geographical coordinates for the clientip.
```geostats```: Generates statistics for mapping data.
Search Query
The query I executed is:
```
host="csvd" index="main" sourcetype="access_combined_wcookie" msg=CreditNotAccepted
| iplocation clientip
| geostats latfield=lat longfield=lon count by Country
```
Result
After running the query, I observed that most declined transactions occur in the United States, providing valuable insight into transaction patterns.
![Screenshot (537)](https://github.com/user-attachments/assets/f65483a6-ba69-4af6-aaf7-212b39acba7d)

---------------------------------------------------------------------------------------------

## Create a Dashboard for Tracking Data

I created a dashboard to track data related to declined card transactions, saving the map visualization as a panel.

Steps Taken
I saved the search results from  Map Visualization as a panel in a new dashboard.
In the Search and Reporting app, I clicked Save As and selected New Dashboard.
I entered the dashboard title as Sayed Zedan and the panel title as Locations.
I set the permissions as needed (optional) and clicked Save to Dashboard.
Verification
To ensure the dashboard was created successfully, I clicked View Dashboard in the prompt and confirmed it appeared under the Dashboards tab in the Search & Reporting app.


![Screenshot (539)](https://github.com/user-attachments/assets/f0e7192a-650b-4674-9bde-5332d0b4167b)

--------------------------------------------------------------------------

## Add a Panel to a Dashboard
Objective
I created a panel called “Transaction History” that displays a timeline of declined transactions for each IP address and added it to the “Sayed Zedan” dashboard.

### Steps

To create a line chart showing the timeline of declined transactions by client IP, I executed the following command:
```
host=WebServer index=main sourcetype=access_combined_wcookie msg=CreditNotAccepted | timechart count by clientip
```


![Screenshot (538)](https://github.com/user-attachments/assets/3db372ac-a3da-4e59-a31f-1534a1d0441e)


------------------------------------------------------------------------------------------------------------------------------------

## Create a Scheduled Report

I created an alert called “Server Error Report” to notify the development team of server-related errors associated with the online store. This alert emails a chronological list of server errors every Monday at 8 AM.

### Steps
I navigated to the Search & Reporting app.

I entered the following search string to create a table containing the requested information:
```
index=* host=WS sourcetype=access_combined_wcookie status>=500 | table clientip, method, referer, status, req_time | sort req_time
```
![Screenshot (540)](https://github.com/user-attachments/assets/3cea130a-8dde-44ec-9056-a7eb6a0566b6)


To automate the reporting, I clicked Save As and selected Alert.

I configured the alert with the following settings:

Title: Server Error (Sayed Zedan)
Alert Time: Run every week, on Monday at 8 AM
Trigger Actions: Send email 
![Screenshot (542)](https://github.com/user-attachments/assets/15da3999-c1ac-4f90-aad7-11c0717d583c)

![Screenshot (541)](https://github.com/user-attachments/assets/3d811658-22db-46a6-96ac-b644e0748ce0)

Finally, I clicked Save to confirm the alert.

Verification
To ensure the scheduled report was created successfully, I clicked View Alert. Alternatively, I checked the details of the newly defined alert under the Alerts tab in the Search & Reporting app.
![Screenshot (543)](https://github.com/user-attachments/assets/cabf7160-df2a-4d0c-81eb-49ef55e067ea)





