# Case-study-2-Streamlining-Operation-with-Google-Script
## Write a script that:
1. Triggers when a Google Calendar event is created
2. Creates a Google Drive folder named "[Participant A's name + Participant B's name Handoff]"
3. Shares the folder with Participant A
4. Monitor a given Google Form link, which will only be submitted by Participant B
5. Upon form submission by Participant B:
- Retrieves and formats the response data into a PDF
- Saves the PDF in the created Drive folder
- Notifies both participants via email
6. Implements error handling and logging

## Deliverables:
1. Commented Google Apps Script code
2. Brief implementation guide

## Implementation Guide
1. Set Up Google Calendar Trigger
- Open Google Apps Script in Google Calendar.
- Add the onCalendarEventCreated function as a trigger for event creation.
2. Set Up Google Form Trigger
- Open Google Apps Script in Google Forms.
- Link the onFormSubmit function as a form submission trigger.
3. Permissions
- The script requires access to Google Calendar, Google Drive, and Gmail.
4. Logging & Error Handling
- All errors are logged in the Apps Script Logger.
- Ensure Google Apps Script execution logs are monitored for troubleshooting.

```// Function triggers upon Google Form submission
function onFormSubmit(e) {
  try {
    var responses = e.values;
    var timestamp = responses[0]; // Extract timestamp
    var data = responses.slice(1).join("\n"); // Format form responses
    
    // Retrieve stored folder ID and access the folder
    var folderId = PropertiesService.getScriptProperties().getProperty("handoffFolderId");
    var folder = DriveApp.getFolderById(folderId);
    var fileName = "Handoff_Report_" + timestamp + ".pdf";
    
    // Create a new Google Document for storing responses
    var doc = DocumentApp.create(fileName);
    var body = doc.getBody();
    body.appendParagraph("Handoff Report");
    body.appendParagraph("Timestamp: " + timestamp);
    body.appendParagraph("Details: \n" + data);
    
    // Convert the document to a PDF and store it in the folder
    var pdf = DriveApp.createFile(doc.getAs("application/pdf"));
    folder.addFile(pdf);
    
    // Retrieve participant emails from stored properties
    var participantA = PropertiesService.getScriptProperties().getProperty("participantA");
    var participantB = PropertiesService.getScriptProperties().getProperty("participantB");
    
    // Send email notifications to both participants
    MailApp.sendEmail(participantA, "Handoff Completed", "The handoff report has been saved: " + pdf.getUrl());
    MailApp.sendEmail(participantB, "Handoff Submitted", "Your submission has been saved: " + pdf.getUrl());
  } catch (error) {
    Logger.log("Error: " + error.message);
  }
}```