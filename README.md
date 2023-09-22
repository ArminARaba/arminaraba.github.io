# Google Forms to Discord & GitHub Project

This project allows automated data submission from Google Forms to Discord and GitHub. It uses Google Apps Script to process form responses and send them to specified Discord webhooks and a GitHub repository. This is particularly useful for applications where quick notifications or a public log of form responses are required.

## Features

- **Form Processing:** Processes responses from Google Form submissions.
- **Customizable String Output:** Allows the creation of customizable strings from form data before sending.
- **Dual Webhook Integration:** Simultaneously sends different customizable messages to Discord and GitHub.
- **Time Delay:** Can include a delay between the messages sent to Discord and GitHub to avoid rate limits or other restrictions.

## Implementation Details

### 1. Form Processing:

The script processes the latest response from the connected Google Form, extracting the needed data such as “Discord announcement,” “Broadcast announcement,” and “FLAGS” based on the titles of the form items.

```javascript
function processFormData(e) {
  const form = FormApp.getActiveForm(),
        allResponses = form.getResponses(),
        latestResponse = allResponses[allResponses.length - 1];
  
  if(!latestResponse) throw "No Responses found in your form.";
  
  const response = latestResponse.getItemResponses(),
        processedData = {};
        
  response.forEach(itemResponse => {
    const title = itemResponse.getItem().getTitle(),
          response = itemResponse.getResponse();
    
    if(title === "Discord announcement") processedData.discordAnnouncement = response;
    if(title === "Broadcast announcement") processedData.broadcastAnnouncement = response;
    if(title === "FLAGS") processedData.flags = response; // Extracting FLAGS field
  });

  return processedData;
}
```

### 2. Customizable String Output:

After processing the form data, the script constructs customizable strings for both Discord and GitHub, embedding the extracted data.

```javascript
const githubContent = `Github Content: ${processedData.broadcastAnnouncement} - Flags: ${processedData.flags}`;
const discordContent = `Discord Content: ${processedData.discordAnnouncement} - Flags: ${processedData.flags}`;
```

### 3. Dual Webhook Integration:

The project features functions `sendToGithub` and `sendToDiscord`, which are responsible for sending the constructed strings to the specified GitHub repository and Discord webhook, respectively.

```javascript
function sendToGithub(textPayload) {
  // ... Sends the payload to GitHub repository
}

function sendToDiscord(textPayload) {
  webhooks2.forEach(webhook => {
    UrlFetchApp.fetch(webhook, textPayload);
  });
}
```

### 4. Time Delay:

To avoid any potential issues due to rate limits, a delay can be incorporated between sending messages to Discord and GitHub.

```javascript
Utilities.sleep(50000); // Wait for 50 seconds
```

## Use Cases

This project can be used in any scenario where there is a need to publicly log form responses or notify a community, such as:

- Event Registration Notifications
- Community Feedback Logging
- User Report Management
- Incident Tracking and Reporting

## Setup and Deployment

1. **Form Creation:**
   - Create a Google Form with required fields like “Discord announcement,” “Broadcast announcement,” and “FLAGS.”
   
2. **Script Linking:**
   - Link the Google Apps Script to the form and paste the provided code.

3. **Webhook Configuration:**
   - Configure Discord webhooks and GitHub API tokens, repo, and branch details in the script.

4. **Deployment:**
   - Deploy the script as a web app and use the provided URL as the form’s submission trigger.

5. **Testing:**
   - Submit a response through the form to test the setup.
