---
sidebar_label: 'Reference Information'
---

# FICS Connector Reference Information

## Strategy

The request for a particular operation can be quite lengthy as there are numerous parameters that can (or must) be set. For example, consider one of the simpler requests. (This is for the "Get Bill and Receipt State- ment Report" operation.) The requests looks like the following:

```json
{
   "Message": {
      "JobId": "sample string",
      "CommentPage": "sample string",
      "CommentLine": "sample string", 
      "IncludeDisbursement": false,
      "IncludeInterest": false,
      "IncludePayInFull": false,
      "IncludeLateCharges": false, 
      "IncludeReturnCheckCharges": false, 
      "IncludeMiscellaneousFees": false, 
      "IncludeBankFees": false, 
      "IncludeAutomatedClearingHouse": false, 
      "IncludePayrollDeduction": false, 
      "SuppressPrintingCompanyName": false,
      "SendEmail": false,
      "SuppressPrintingCommentPage": false, 
      "SuppressPrintingDelinquencyNotice": false, 
      "FilePath": "sample string", 
      "BeginningTransactionDate": "2015-03-03T15:25:46", 
      "EndingTransactionDate": "2015-03-03T15:25:46", 
      "IncludeLoansDueOn": "2015-03-03T15:25:46", 
      "SystemDate": "2015-03-03T15:25:46",
   "Token": "sample string"
   } 
}
```

Additionally, the number (and composition) of a request can change as new releases of Mortgage Servicer are released. In order to provide a flexible mechanism that allows users to manage parameter changes (without requiring new FICS/OpCon connector versions), the following approach is recommended.

1. The user opens the API documentation for FICS (using a web browser). This website URL will be dif- ferent for different customers (and can be supplied by FICS).
2. The user locates the operation to be performed (such as Billing->Bill and Receipt). Expand the "Example Request" link.
3. Copy the request (hold down the left mouse button, highlight the text, and press Control+C) and paste it in a text file.
4. The user can then edit the text file directly or can use the SMAFICSTemplateEditor to edit the file. If the user edits the file using a tool such as Notepad, it is imperative that the proper format and con- struction be maintained. If SMAFICSTemplateEditor is used, it will automatically maintain the format.
5. The values to use can be determined by manually walking through the process using Mortgage Ser- vicer and recording the values (and field labels) that are used. These values can then be entered into the text file created in the step above. Dynamic values can be replaced by OpCon properties (entered in the normal [[Property Name]] format). For example, dates would most likely be calculated by another process and stored as a property. Then, any FICS operation that needed that date could reference the OpCon property.
6. Set up an OpCon job to execute SMAFICSConnector. The command line must specify the name of the text file (or template) and the operation to be performed.

## Notes on DateTime Handling

1. Some of the processes excuted by FICS require that the same time stamp be used for each of them. For example, in processing the daily report bundle, you must use the same time stamp for the update cycle for successful completion. Instead of trying to capture the timestamp used from the response, consider the following strategy:
* Create a global property called FICS_Timestamp.(The initial value is not important.)
* Create a global property called $NOWYYYY-MM-DDhh:nn. Its value should be: YYYY-MM-DDhh:nn
* Create a Null job with one event. The event should be:
   * $Property:Set,FICS_TimeStamp,$NOWYYYY-MM-DDhh:nn 
   * Set this job to run at the very beginning of each processing day.

* Now, anytime there is a need for timestamp coordination, use [[FICS_Timestamp]] in the request files.

2. Always use the –SystemDate command line parameter for SMAFICSConnector jobs. If reruns are
ever required, this will greatly facilitate the process. 

:::tip

If you set up the FICS_Timestamp as described above, you can specify –SystemDate=[[FICS_Timestamp]] on every job).

:::