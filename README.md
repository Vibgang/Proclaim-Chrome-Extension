# Data Capture Chrome Extension
## Overview
This Chrome extension is designed to extract data from specific web page rows, store it locally, and send the data to a Google Sheet. It simplifies data collection, management, and validation by integrating directly with web pages and Google Sheets.

## Features
Data Extraction: Captures data such as Case ID, Bill Number, Hospital Name, Claimed Amount, Bill Amount, NME Amount, Discount, and NME Percentage from web page rows.
Local Storage: Stores captured data temporarily in the browser’s local storage for user review.
Duplicate Check: Verifies data against existing Case IDs in Google Sheets to avoid duplication.
Submission to Google Sheets: Sends valid data to a Google Sheet via a Google Apps Script web app endpoint.

### Installation:
Clone or download this repository.
Open Chrome and navigate to chrome://extensions/.
Enable Developer Mode (toggle in the top-right corner).
Click Load unpacked and select the project folder.

### Files and Structure:
manifest.json: Specifies the extension's configuration, permissions, and content scripts.
contentScript.js: Main logic for data extraction, local storage, duplicate checking, and sending data to Google Sheets.
background.js, popup.html, popup.js: Currently empty but reserved for future background and UI functionalities.

### How It Works
1. The script identifies rows and extracts data like Case ID, Bill Number, and more.
2. Data is stored in local storage when the “Tabulate” button is clicked.
3. A dynamically created Send Data button allows users to submit data to Google Sheets.
4. The extension checks for duplicates by comparing with existing Case IDs.
5. The extension sends a POST request to a Google Apps Script endpoint connected to a Google Sheet.
If the Case ID exists, the data is not sent, preventing duplicate entries.

#### Permissions
activeTab: Allows the extension to interact with the active web page.
storage: Enables storing data in Chrome's local storage.
scripting: Required for content script injection.

### Setting Up the Google Apps Script
Open Google Apps Script.
Create a new project and paste the following code:

     function doPost(e) {
    
        var sheet = SpreadsheetApp.openById('YOUR_SPREADSHEET_ID').getSheetByName('Sheet1');
    
        var data = JSON.parse(e.postData.contents);
    
        sheet.appendRow([
        data.timestamp,
        data.caseId,
        data.billNumber,
        data.hospital,
        data.duration,
        data.claimedAmount,
        data.billAmount,
        data.nmeAmount,
        data.discount,
        data.nmePercentage,
        data.timespent
        ]);
    
        return ContentService.createTextOutput(JSON.stringify({ 'result': 'success' })).setMimeType(ContentService.MimeType.JSON);
    }

Deploy the script as a Web App and copy the deployment URL.

Replace the URL in contentScript.js with your web app URL.

### Usage:
Navigate to the relevant webpage containing data rows.
Click the "Tabulate" button to capture data.
Use the Send Data button to submit data to Google Sheets.
Check for data submission success or duplicate alerts in the console.

#### Troubleshooting:
Ensure the Google Apps Script URL is correct and accessible.
Verify that the spreadsheet ID in the Apps Script matches your sheet.
Enable permissions if Chrome prompts for them.
