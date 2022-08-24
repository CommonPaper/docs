# docs
Technical documentation

## Webhooks
The Common Paper webhook notifications allow you to extend the activity around your agreements to other parts of your organization.

### What can you do with webhooks?
There are a wide variety of uses for webhooks, but the easiest way to get started with them is to use a service like Zapier.  This tool can accept incoming webhooks like the ones sent from Common Paper, and can then use that information to quickly and easily push it into other tools and services.  

Some examples of what you could do include:

- Send updates about your agreements to a common Slack channel
- Automatically email documents to a recipient after an NDA has been signed
- Notify your attorney if a recipient proposes changes
- Update your CRM when an agreement is sent or signed

### How to Configure Webhooks
In the Settings section in your Common Paper account, you can configure webhooks to fire each time something happens during the agreement flow.  All that’s needed is the URL endpoint where you would like the notifications sent.

### Helpful Hints for Webhook Notifications

- In general, the event_type field is more useful and consumable than the status field, so it’s recommended to use that field for any logic for automating actions
- The URL to the agreement will require a Common Paper login, and so can’t be used to give access to an agreement to a third party
- The recipient organization field is not available until after a recipient has seen and acted on an agreement
- The event description is exactly the same as what is seen in the History view of each agreement in the application.

#### Payload
Each event fired contains the same payload of information from Common Paper.

| Fields                  | Description                                                                                                              | Type      |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------ | --------- |
| agreement\_id           | Unique identifier for the agreement                                                                                      | UUID      |
| agreement\_type         | Agreement Type (NDA, CSA, etc.)                                                                                          | String    |
| template\_name          | Name of the template that the agreement is based on                                                                      | String    |
| url                     | Link to the agreement in Common Paper (requires login)                                                                   | String    |
| sender\_email           | Email address of the Common Paper user that sent the agreement                                                           | String    |
| sender\_name            | Name of the Common Paper user that sent the agreement                                                                    | String    |
| recipient\_organization | Organization name supplied by the recipient.<br><br>NOTE: This will be blank until a recipient has supplied it.          | String    |
| recipient\_email        | Email address of the recipient that is currently associated with the agreement                                           | String    |
| recipient\_name         | Name of the recipient that is currently associated with the agreement                                                    | String    |
| status                  | Status of the agreement AFTER the event                                                                                  | String    |
| event\_date             | Date and time the event occurred (UTC)                                                                                   | Timestamp |
| event\_type             | Short description of the event                                                                                           | String    |
| event\_description      | Long description of the event that includes a plain text description of what happened, often including who was involved. | String    |  
|recipient_organization|NOTE:  This will be blank until a recipient has supplied it.|String|
|recipient_email|Email address of the recipient that is currently associated with the agreement|String|
|recipient_name|Name of the recipient that is currently associated with the agreement|String|
|status|Status of the agreement AFTER the event|String|
|event_date|Date and time the event occurred (UTC)|Timestamp|
|event_type|Short description of the event|String|
|event_description|Long description of the event that includes a plain text description of what happened, often including who was involved.|String|

### Example JSON
Here’s an example payload sent after a new agreement is created.

```
{
"agreement_id":"20af5c12-ea91-446c-b80d-aaafb09b1a21",
"agreement_type":"NDA",
"template_name":"Company Dot Com Mutual NDA Template",
"url":"http://app.commonpaper.com/agreements/20af5c12-ea91-446c-b80d-aaafb09b1a21",
"sender_email":"jerry@company.com",
"sender_name":"Jerry",
"recipient_organization":”Vandelay Industries”,
"recipient_name":"George",
"recipient_email":"george@vendelay.com",
"status":"sent_waiting_for_initial_review",
"event_type":"sent",
"event_date":"2022-08-18T17:56:29.018Z",
"event_description":"Jerry (jerry@company.com) sent the agreement to George (george@vendelay.com)."
}
```

### Events
When configured with an endpoint, Common Paper will send the event information when the following events occur.


| Event | Action | Description|
| ----- | ------ | ---------- |
| Sender sends agreement                    | sent                            | \[Sender\] sent the agreement to \[recipient\]<br><br>Included Comment \[if any\].                              |
| Sender reassigns to new recipient         | reassign                        | \[Sender\] reassigned the agreement from \[recipient 1\] to \[recipient 2\]<br><br>Included Comment \[if any\]. |
| Sender voids agreement                    | voided                          | \[Sender\] voided the agreement.                                                                                |
| Recipient views agreement                 | viewed                          | \[Recipient\] viewed the agreement.                                                                             |
| Recipient reassigns to a different person | reassigned                      | \[Recipient\] reassigned the agreement to \[new recipient\].<br><br>Included Comment \[if any\]                 |
| Recipient declines agreement              | declined                        | \[Recipient\] declined the agreement.<br><br>Included Reason \[if any\]                                         |
| Recipient signs agreement                 | signed                          | \[Recipient\] signed the agreement.                                                                             |
| Sender/signer signs the agreement         | signed                          | \[Signer\] signed the agreement.                                                                                |
| Recipient proposed changes                | proposed changes                | \[Recipient\] proposed changes to the agreement.                                                                |
| Sender counter proposes changes           | proposed changes                | \[Sender\] proposed changes to the agreement.                                                                   |
| Sender sends reminder email               | sent reminder                   | \[Sender\] sent \[recipient\] an email reminder.                                                                |
| Recipient downloaded PDF version          | downloaded PDF                  | \[Recipient\] downloaded a PDF of the agreement.                                                                |
| Recipient downloaded DOCX version         | downloaded Word document (DOCX) | \[Recipient\] downloaded a Word document (DOCX) version of the agreement.                                       |
| Sender sent agreement for signature       | sent for signature              | \[Sender\] sent the agreement to \[Recipient\] for signing.                                                     |
| Both parties have signed                  | completed                       | Everyone has signed this agreement.                                                                             |

### Statuses
These are very granular statuses for agreements, and represent the state of an agreement AFTER the event in the payload occurs.  When using webhooks, it’s advised to use the ‘event_type’ field for most use cases.

| Status | Description |
| ------ | ----------- |
| sent_waiting_for_initial_review | The agreement has been sent to the recipient and they haven't taken any actions yet. |
|sent_waiting_for_sender_review | The agreement has been sent back to the sender for review.|
| sent_waiting_for_recipient_review | The agreement has been sent back to the recipient for review after some changes have been made.|
| sent_to_sender_signer_for_signature | The agreement has been accepted by the sender owner, and sent to the sender signer to sign.|
|sent_to_hellosign_by_recipient_initial_review|The agreement has been sent to the signing service without any modifications from the original terms, but has not yet been signed.|
|sent_to_hellosign_by_recipient| The agreement has been sent to the signing service by the recipient after some modifications were made, but not yet signed.|
|sent_to_hellosign_by_sender| The agreement has been sent to the signing service by the sender after some modifications were made, but has not yet been signed.|
|waiting_for_sender_signature|The recipient has signed and is waiting on the sender to sign.|
|waiting_for_recipient_signature|The sender has signed and is waiting on the recipient to sign.|
|signed|Both parties have signed the agreement|
|signed_waiting_for_final_confirmation|Both parties have signed, but we haven't received the all-signed webhook event from the signing service yet.|
|declined_by_recipient|The recipient has declined the agreement.|
|voided_by_sender|The sender has voided the agreement.|
