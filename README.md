# **Project Documentation: Candidate Submission Page**

## **1\. Introduction**

This document provides a complete overview and setup guide for the **Candidate Submission Page** project. The purpose of this system is to provide a user-friendly, web-based form for job candidates to submit their personal information, education history, work experience, and a profile photo.

All submitted data is automatically organized and stored in a Google Sheet, with photos uploaded to a corresponding Google Drive folder, creating a simple and effective Candidate Relationship Management (CRM) system.

## **2\. System Components**

The project consists of three core components that work together:

### **a. The Frontend: HTML Form (index.html)**

This is the public-facing webpage that candidates interact with.

* **Technology:** Built with HTML, Tailwind CSS for styling, and vanilla JavaScript for interactivity.  
* **Features:**  
  * Clean, professional, and fully responsive design.  
  * Input fields for personal details (Name, Email, Phone).  
  * A file uploader for a profile photo.  
  * Dynamic sections for **Education** and **Work Experience**, allowing candidates to add or remove multiple entries.  
  * Client-side validation and a user-friendly submission process with status modals (loading, success, error).

### **b. The Backend: Google Apps Script**

This script acts as the server and the bridge between the HTML form and your Google Workspace.

* **Technology:** Google Apps Script (based on JavaScript).  
* **Binding:** The script is **bound** to the destination Google Sheet, which is how it knows where to save the data without needing an explicit ID.  
* **Functions:**  
  * doPost(e): This is the main function that runs when the form is submitted. It receives the form data, parses it, and orchestrates the saving process.  
  * saveImageToDrive(...): This helper function decodes the base64 image string sent from the form, saves it as a JPG file in a specified Google Drive folder, and returns its public URL.

### **c. Data Storage: Google Sheets & Google Drive**

This is where all the candidate information is stored.

* **Google Sheet:**  
  * Acts as the primary database.  
  * Each form submission creates a new row.  
  * Complex data (multiple education/work entries) is stored as a structured JSON string within a single cell for easy readability and parsing.  
* **Google Drive:**  
  * A folder named "Candidate Photos" is automatically created.  
  * Each uploaded photo is stored here, named after the candidate.  
  * The link to the photo in Google Drive is placed in the corresponding row in the Google Sheet.

## **3\. Setup and Installation Guide**

Follow these steps to deploy the system.

### **Step 1: Prepare the Google Sheet**

1. Open the target Google Sheet.  
2. Create a new tab (or rename an existing one) to **Candidates**.  
3. In the first row of this sheet, set up the following headers exactly as written:  
   Timestamp | Full Name | Email | Phone | Photo | Education | Work Experience

### **Step 2: Create and Deploy the Google Apps Script**

1. In your Google Sheet, navigate to **Extensions \> Apps Script**.  
2. Delete any placeholder code and paste in the provided script code.  
3. Click the **Save project** icon.  
4. Click the **Deploy** button and select **New deployment**.  
5. In the deployment settings:  
   * **Type:** Select Web app.  
   * **Execute as:** Select Me.  
   * **Who has access:** Select Anyone.  
6. Click **Deploy**.  
7. **Authorize** the script by granting it the necessary permissions for Google Sheets and Google Drive. You may need to click "Advanced" and "Go to... (unsafe)" to proceed.  
8. After deployment, **copy the Web app URL**. This is crucial for the next step.

### **Step 3: Configure the HTML Form on GitHub**

Since your project is hosted on GitHub, you'll need to update the index.html file directly in your repository to connect it to your new Google Apps Script.

1. **Navigate to Your Repository:**  
   * Go to [GitHub](https://github.com) and log in to your account.  
   * Find and open the repository where your index.html file is stored.  
2. **Open the HTML File:**  
   * In the list of files, click on index.html to view its content.  
3. **Enter Edit Mode:**  
   * On the file view page, click the **pencil icon** (Edit this file) in the top-right corner of the file content area.  
4. **Update the Script URL:**  
   * Scroll down to the bottom of the file to find the \<script\> section.  
   * Locate this line:  
     const scriptURL \= 'YOUR\_WEB\_APP\_URL';

   * Carefully delete YOUR\_WEB\_APP\_URL and paste the new **Web app URL** you copied from your Google Apps Script deployment. The line should now look like this (with your actual URL):  
     const scriptURL \= 'https://script.google.com/macros/s/ABCD.../exec';

5. **Commit the Changes:**  
   * Scroll to the bottom of the page to find the "Commit changes" section.  
   * In the first input box, you can type a brief description of your change, for example: Update Google Apps Script URL.  
   * Ensure the "Commit directly to the main branch" option is selected.  
   * Click the green **Commit changes** button.

Your website will automatically update with the new changes. The form will now send data to the correct, newly deployed script.

## **4\. Data Flow (How It Works)**

1. **Submission:** A candidate fills out the form and clicks "Submit Application".  
2. **Data Collection:** The JavaScript in the HTML file gathers all the data from the input fields, converts the photo to a base64 string, and bundles everything into a single JSON object.  
3. **API Call:** A fetch request is sent from the browser to your unique Google Apps Script **Web app URL**. The JSON object is sent as the body of this POST request.  
4. **Script Execution:** The doPost(e) function in your Apps Script is triggered.  
5. **Data Processing:** The script parses the JSON data, calls the saveImageToDrive function to handle the photo, and maps the remaining data to the correct columns based on the headers in your Google Sheet.  
6. **Storage:** The script appends a new row to the 'Candidates' sheet and writes the collected data, including the URL of the newly saved photo.  
7. **Response:** The script sends a success or error message back to the HTML form, which then displays the appropriate message to the candidate in the modal.
