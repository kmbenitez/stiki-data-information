#Document Documentation

## Google Docs documentation

[Transferring Google Docs ownership](https://support.google.com/a/answer/1247799?hl=en)

- Reply Manager - Intro to the feature for clients, with screenshots
- Reporting Overview - My notes about reporting processes, created before a vacation. Can't tell when last updated. An updated version will be available in a github repository.
- StikiSend API - Initial documentation. Out of date.
- Database Information - Partial documentation of some database tables and functionality. Last updated September, 2018. Updated version will be available.
- Retargeting Project - Working out specs for a project idea. Didn't happen
- 1Touch - Describes the project
- Stiki Order Notification Documentation - Documentation for initial implementation of Order Notification API, created before me

## API documentation

Documentation available to clients is stored on S3. Internal documentation of the API is in the `README` files in their repositories.

- **StikiNotify**
	- [public docs](https://s3.amazonaws.com/stikimedia/docs/StikiNotifyDocs.html)
	- [internal docs](https://github.com/solution-consulting/stiki-notify-api/blob/master/README.md)
- **StikiSend**
	- [public docs](https://s3.amazonaws.com/stikimedia/docs/StikiSend.html)
	- [internal docs](https://github.com/solution-consulting/stiki-send-api/blob/master/README.md)


## 3rd party documentation

I have Postman collections created from exploring most of these APIs.
- [Skycore API](http://apidocs.skycore.com/) [Postman collection](https://www.getpostman.com/collections/646778cc4220fa780131)
- [TimeZoneDB API](https://timezonedb.com/api) [Postman collection]()
- [Response API](https://leadsapi.idbcore.com/leads/doc/) - We use two components of this--Event and Leads, but the version number keeps changing, so go to this central page, then click through to the relevant page.
	- Event [Most current URL for Event](https://leadsapi.idbcore.com/leads/doc/1.5.57/event/get.html) [Postman collection for Event](https://www.getpostman.com/collections/c37c336c6baa56855764)
	- Leads [Most current URL for updating leads](https://leadsapi.idbcore.com/leads/doc/1.5.57/lead/update_lead.html) [Postman collection for Leads](https://www.getpostman.com/collections/784226718a7104c544c8)
- [Periscope Embed API](https://doc.periscopedata.com/article/embed-api) [More info](https://doc.periscopedata.com/article/embed-api-options)
