# KPI-Excel-Generator
Tool used to collect metric data from AppDynamics and organize it within an Excel document, in addition to computing new KPI's. 

**Awaiting approval to publish project code**

**Background Information:**  
AppDynamics contains a wealth of information on the performance of [redacted] systems. It records data on the various transactions that take place, including general performance, response time, call stack information, etc. Of the many components that make up Cubic's systems, we are most interested in [redacted], as it directly relates to the customer.

**Summary:**  
The KPI Report works as follows:
1) A time range is provided by the user. This time range is used to determine what data to collect from AppDynamics.
2) The script makes several API calls to AppDynamics that return all non-healthy snapshots related to [redacted]
3) Each snapshot is examined, and a list of the "problematic" method calls, slow exit calls, and errors are returned (referred to as _problems_).
5) The call stack of the snapshot (and any associated call stacks) is searched for each problem collected previously, and metric data on each problem is collected and saved.

The Data collected by the script is saved to an Excel document. The document is broken down as follows:

- Data related to each tier is saved to a separate sheet. In addition, there is an _Overview_ sheet containing at-a-glance information on the overall document. An _Errors and Slow Exit Calls_ sheet contains the full error or slow exit call methods that are referenced in the other sheets. This is also the case for the _GUID_ sheet, which contains hyperlinked GUIDs associated with a their corresponding _problem_.

- Each sheet holding tier data presents all the _problems_ associated with that tier. Metric data for each problem is displayed along the same row as the problem. See _fetchComponentProblems.py_ for a description of the metrics recorded and what they mean.

- The _Overview_ sheet summarizes the document by breaking down the number of problems in each tier. In addition, it categorizes all the problems in the document and presents the top 10 problems for each category (e.g., top 10 most frequently occurring errors).

**Before Running the Tool**  
You need to create a .env file and add it to the directory. The .env file contains the necessary credentials to access your respective AppDynamics account via API calls.

**Running the Tool**:  
Currently, the tool is run by doing the following:
1) At the bottom of the _fetchComponentProblems.py_ script, copy the name of the Blazemeter test you are running the script for along with parameters of a BlazeMeter tests you want to run the tool for into a list (an example is provided in the script). The expected format is ["quarter build start_date start_time end_date end_time"], where quarter, build and start_date are separated by tabs, and the rest is separated by a space.
2) Run the script
3) An excel doc is generated in a folder matching the name of the BlazeMeter tests, where the excel doc is named by the BlazeMeter tests and the startdate and time.

**AppDynamicsCalls.py**  
Used internally to make API calls to AppDynamics. Some of the API calls used can be found in the documentation, while
others were found by inspecting the html elements of AppDynamics in the browser. The API calls are made using the _requests_ library.

**AppDynamicsProcessor.py**  
Used internally and holds common functions used by several different files and is used to process the data retrieved from AppDynamics.

**CallStackAnalyzer.py**  
Used to retrieve the call stack data from AppDynamics and records data to an Excel file on the call relationship between methods.  This is 
used to create the edges in the nodes & edges visualization. It also saves data related to the methods themselves to an Excel file. 
This is used to supplement the node data in the nodes & edges visualization.

**fetchComponentProblems.py**  
Used to fetch the potential problems from AppDynamics and organize & store them in an Excel file. It also records node
related data into a second Excel file.

**mergeExcelFiles.py**  
Runs **CallStackAnalyzer** and **fetchComponentProblems** for a given time range. It merges the Excel documents produced that contain node related
data into one excel document. It also produces another Excel document containing relationship data between a file and its methods.
