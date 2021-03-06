#Additional Reporting Information

## Daylight Savings Time
Stiki logs are recorded in UTC, but most reporting is done in EST or EDT. Time changes in the fall or spring might affect the following systems:

- *Generic Dashboards* - If clients are paying particularly close attention to their dashboards, they may notice minor changes to their information, as reported in Periscope. All client dashboards are set to change UTC to Eastern time, and Periscope determines how to do that based on the current time delta (4 hours in EST, 5 hours for EDT). Therefore, when looking at historical data, texts around midnight may show up assigned to a different day than they did originally. Explaining this generally falls to the account managers.

- *Billing* - Unlike the client dashboards, the billing dashboards are set up to use the more intensive 'historical' conversion betweeen UTC and Eastern time. This means that whatever day a text or click is assigned to should be correct on the billing dash. However, the daterange filter on the dashboard doesn't do this historical conversion. Therefore, any chart which divides results up into days is accurate, but the daterange filter must be set to include additional days to make sure all the correct records are pulled.

>*Example of Billing Issue* \
>Diagram can be found [here](https://mermaidjs.github.io/mermaid-live-editor/#/view/eyJjb2RlIjoiZ3JhcGggTFJcbkFbVGV4dCBhcnJpdmVzPGJyLz4xMToyMyBwbSBFRFQsIE1hcmNoIDc8YnIvPjIwMTktMDMtMDggMDQ6MjMgVVRDPGJyLz5dICBcbkJbMTE6MjMgcG08YnIvPk1hcmNoIDddXG5DWzEyOjIzIGFtPGJyLz5NYXJjaCA4XVxuRFsxMToyMyBwbTxici8-TWFyY2ggN11cbkVbUGVyaXNjb3BlIGRhdGVyYW5nZSBmaWx0ZXI8YnIvPjMvNy0zLzddXG5GW3RpbWVzdGFtcCBiZXR3ZWVuPGJyLz4gMjAxOS0wMy0wNyAwNTowMDowMCBhbmQ8YnIvPiAyMDE5LTAzLTA3IDE5OjAwOjAwXVxuR1t0aW1lc3RhbXAgYmV0d2VlbiA8YnIvPjIwMTktMDMtMDcgMDQ6MDA6MDAgYW5kIDxici8-MjAxOS0wMy0wOCAwNDowMDowMF1cbkh7VGV4dCBpcyBhc3NpZ25lZCB0byBjb3JyZWN0IGRheSw8YnIvPmJ1dCBmaWx0ZXIgYXBwbGljYXRpb24gcHJldmVudHM8YnIvPiBpdCBmcm9tIGJlaW5nIGluY2x1ZGVkfVxuQS0tPnx2aWV3ZWQgYmVmb3JlIHRpbWUgY2hhbmdlfCBCXG5BIC0tPnx2aWV3ZWQgYWZ0ZXIgdGltZSBjaGFuZ2V8IENcbkEgLS0-fHZpZXdlZCBpbiBiaWxsaW5nfCBEXG5FIC0tPnxhcHBsaWVkIGJlZm9yZSB0aW1lIGNoYW5nZXxGXG5FIC0tPnxhcHBsaWVkIGFmdGVyIHRpbWUgY2hhbmdlfEdcbkQtLT5IXG5HLS0-SFxuc3R5bGUgSCBmaWxsOndoaXRlLHN0cm9rZTpyZWQsc3Ryb2tlLXdpZHRoOjNweFxuY2xhc3NEZWYgdGV4dCBmaWxsOnBlYWNocHVmZixzdHJva2U6b3JhbmdlXG5jbGFzcyBBIHRleHRcbmNsYXNzIEIgdGV4dFxuY2xhc3MgQyB0ZXh0XG5jbGFzcyBEIHRleHQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9fQ)
>Text arrives at 11:23 pm, March 7. \
>Stored in records as 2019-03-08 04:23 UTC. \
>Date range filter asked to find data for March 7 searches for
records with timestamps between 2019-03-07 04:00:00 and 2019-03-08 04:00:00
>Text is not included in results
>Date range filter asked to find data for March 6 and 8 searches for records with timestamps between 2019-03-06 04:00:00 and 2019-03-09 04:00:00
>Text is included in results. If historically accurate timestamp conversion is used, date is reported as March 7. If standard timestamp conversion is used, date is reported as March 8.

- *Programs* - This isn't reporting, but is worth noting. We currently have a daily report for Barrington generated by a script running on an EC2 instance known as CronRunner. This script has the UTC -> Eastern offset hardcoded. It is on the development list to eliminate this dependency (and eliminate the CronRunner EC2 instance entirely, for Lambdas run on CloudWatch events).