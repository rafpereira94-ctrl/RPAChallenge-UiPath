# RPA Challenge - UiPath
This project automates the RPA Challenge website using UiPath.

This automation will:
- Download the Excel input file available at https://rpachallenge.com
- Read and process through the table data
- For each row: 
   - Dynamically identify and populate the form fields present on the website
   - Submit the transaction
	
- Track successful and failed transactions
- Perform execution logging and exception handling
- Perform environment cleanup at startup and end of execution to maintain compliance

## Architecture
1. Initialization:
- Environment cleanup
- Download input file
- Read input table and normalize the data

2. Process:
- Populate form fields dynamically
- Transaction tracking and logging

3. Finalization:
- Execution validation
- Cleanup operations
- Final execution summary logging
	
4. Global Exception Handling:
- Centralized unexpected exception handling
- Emergency cleanup
- Failure escalation support and notification

## Assumptions
- https://rpachallenge.com is available
- Chrome browser is installed locally and UiPath extension is installed
- Internet connectivity is stable
- The input Excel suffers no changes

## Technical Decisions
### Input Data
- To improve execution time the automation will download the input Excel through it's URL
- During initial testing it was noticed that the column names of the input table contained inconsistent spacing, to combat that the automation will trim and lowercase all column names to minimize potential issues

### UI Automation
- Given the changes in form field position and order after each submission the automation requires robust selectors which can be easily achieved thanks to modern activities including fuzzy selectors supported by anchors, that deliberately avoid problematic tags or reliance on position
- The automation relies on simulate typing and clicking which allow the automation to run completely out of focus reducing the possibility for issues and improving performance

### Retry Strategy
The website offers very little feedback on each submission but the very last and the challenge restarts upon page refresh as some traditional retry strategies such as relaunching the browser aren’t a good fit, retries were left on an individual activity level which is already covered by modern activities

## Logging
The automation logs include:
- Execution start/end
- Input file handling
- Submission progress
- Final summary
- Exception information

## Requirements
- UiPath Studio 2024+
- Google Chrome installed
- UiPath Browser Automation 24.10 browser extension installed
- Windows 10/11 environment
- Internet connection

UiPath packages:
- UiPath.System.Activities
- UiPath.UIAutomation.Activities
- UiPath.Excel.Activities
- UiPath.Testing.Activities
- UiPath.MicrosoftOffice365.Activities

## Instructions
1. Clone the repository
2. Open the project in UiPath Studio
3. Restore project dependencies
4. Open Main.xaml
5. Run the workflow

## Considerations, Comments and Upgrade Opportunities
The following section contains additional commentary on the project and reflections:
- Email notification is included but not fully configured, I started working on them until I felt I may be overengineering and going out of scope for the exercise, I debated removing them but since they were already created I simply mocked them, to improve upon it I would look to attach the exception screenshots to the relevant emails
- Given the explicit ask to use UI automation and to iterate through the input rows I reached the solution presented, from what I found it appears the submissions are kept completely client side with no API calls being done as such it might not be possible to completely remove UI automation, even so it may be possible to reduce the number of steps by abstracting the Type Into activities further but that might not bring significant performance improvement
- If parts of the automation were to be found reusable across more projects, it would make sense to achieve further abstraction and create separate workflows and possibly add them to a library
- I elected to keep this solution as a sequential process, it could be expanded to use REFramework as each row can easily be worked as a separate transaction, but the increased overhead and complexity didn’t seem justified for this specific challenge
