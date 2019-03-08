<!----- Conversion time: 1.356 seconds.


Using this Markdown file:

1. Cut and paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β16
* Fri Mar 08 2019 08:29:40 GMT-0800 (PST)
* Source doc: https://docs.google.com/a/getstiki.com/open?id=1Kldd_RGHpm6lmCIqC6B_ozl3BhZ_P0dKJjotXDgjskg
* This document has images: check for >>>>>  gd2md-html alert:  inline image link in generated source and store images to your server.


----->

<p style="color: red; font-weight: bold">>>>>>  gd2md-html alert:  ERRORs: 0; WARNINGs: 1; ALERTS: 1.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>>  gd2md-html alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p><a href="#gdcalert1">alert1</a>

<p style="color: red; font-weight: bold">>>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>




<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Stiki-Reporting0.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/Stiki-Reporting0.png "image_tooltip")


**getstiki.com**


# Reporting Overview


### **December 14, 2017**


## OVERVIEW

Setup is different for Inbound and Outbound Campaigns, but both consist of three basic steps: setting up a new campaign in the database, updating the dashboards, and creating a login to the reporting portal.


### INBOUND


## SETTING UP A NEW CAMPAIGN IN THE DATABASE

Sandy typically sends a dashboard creation/update request enumerating the client name, the launch date, the product name, and the associated campaign or campaigns. You'll also need the skycore campaign ids, either from Skycore, or from whoever set up the campaign.


### Automatic



1. Login to reports.getstiki.com. Open the Admin Panel from the links on the upper right in green.
2. Is setup for a current client? If not, create a new client (Client Management -> Create Client)
    1. Client name as given by Sandy.
    2. Dashboard ID = 200211[^1]


    3. Default Daterange = 30
    4. Daterange Visible checked.
    5. Overriding URL blank.

    Click 'Create Client' and look for success or failure message. If the client created is differently named than you wanted (sometimes happens with special characters), note it so that you can change it later. To do so, see the 'Manual' instructions.

3. Create campaigns (Campaign Management -> Create Campaign)
    6. ID (Skycore) = The Skycore campaign id
    7. Campaign Name = ProductName: Keyword to Shortcode (eg ZQuiet: Bed to 246810)
    8. Client Name = provided by Sandy, choose from dropdown menu
    9. Launch Date = provided by Sandy

    Click 'Create Campaign' and look for success or failure message. Again, if the campaign name is not what you wanted, then refer to the manual instructions.



### Manual

You may need the manual instructions if there are a bunch of campaigns to set up at once and you'd rather just use SQL, if there are special characters in the client or campaign name, or for other situations like the media companies (Barrington, Hybrid), who occasionally want logins for their clients. The setup information required is mentioned in the description of the 'Automatic' setup, but includes: Client name, Campaign id, Launch Date, Product name, shortcode and keyword. Work is done in the getstiki database on AWS.



1. Is it for a current client? If so, note the client id. If not, create a new client in the clients table of the database. Some default values:
    1. Order_notification = 0
    2. Active = 1
    3. Dashboard_id = 200211[^2]


    4. Url = 0
    5. Created = current timestamp
    6. Default_daterange = 0
    7. Daterange_visible = 1
    8. Aggregation = (empty string)
    9. Aggregation_visible = 0

    Note the id of the newly created client.

2. Create a campaign in the campaigns table of the database. It should have the client_id  of the client created/noted above. Other values:
    10. shortlink_id = optional
    11. Mc_campaign_id = [campaign id on Trello card]
    12. Sc_campaign_id = [campaign id on Trello card]
    13. Name = ProductName: Keyword to Shortcode [from title of Trello card]
    14. Created = current timestamp
    15. Order_notification = 0
    16. Active = 1
    17. Dest_number = shortcode
    18. Keyword = keyword
    19. participTVSquared = 0
    20. tvs_collectorId = 0
    21. tvs_siteId = 0
3. Add the correct filters to the filters table.  - Filters are automatically created when a client is created, and are client specific. In the default case, this works fine. However, it is helpful to understand the structure of the table.
    22. Client_id = the id of the client associated with the user viewing the dashboard.
    23. Name = the name of the filter
    24. Value = the value provided to Periscope to use the filter
    25. Visible = whether the filter should be made visible to the client and its user(s)
    26. Group = required for child filters, this gives the parent value.

    For inbound campaigns, we have Stiki_Clients filters (value is the client name), which should not be visible, and Client_Campaigns filters (value usually blank), which should be. Client_Campaigns is a child filter, so it needs to know what the parent value is, so the client name is specified in the group field. These are visible because clients can pick and choose which of their campaigns to view, but client is not so that they can't view the data that belongs to other clients.



### OUTBOUND

There is no automatic option for creating outbound campaigns in the database. That's okay for now, because it's actually easier to use the campaign setup spreadsheet that Sandy and Jessica create and do it manually, to save a bunch of repetition.


#### Semi-Automated Workflow for Response



1. Jessica will send you an awesome Excel doc with a sheet for each of the events that we are publicizing that week. (in the example file, the last four sheets of the document)
2. I create a "cover sheet" with just the summary information I need: the sc_campaign_id, keyword, shortlink ID 1, shortlink ID 2, and mmsid1 (in the example file, the first sheet)
3. I then add the personality for each event in a column to the left of all those
4. I add an additional left column and use excel's concatenate function to build two queries for each event-- one which adds information to the outbound_campaigns table, and one which adds information to the outbound_summary table. Note that the below segment is the formula for the second sheet in the table (I use headers), and for the following order of fields: SQL, personality, campaign id, keyword, shortlink ID 1, shortlink ID 2, and mmsid1.

    ```
=CONCATENATE("Insert into outbound_campaigns (client_id, name, created, launch, Active) VALUES (82, '",B2,": ",D2," to 72346', now(), '2018-03-09', 1);insert into outbound_summary (groupNumber, sc_campaignId, stiki_campaignID, sendTime, endTime,mms1_shortlinkID, mms2_shortlinkID,billingMmsid) VALUES (1, ",C2,",last_insert_id(),'2018-03-09 20:00:00', '2099-12-31 00:00:00',",E2,",",F2,",",G2,");")
```



    I update these queries in two places to reflect the updated launch date, and the updated sendTime. Then I copy that function down the page. I copy the whole set of statements and execute them on the database (after sanity checking them)



#### Otherwise



1. Is this for a current client? If not, create the client using the steps described above in the manual or automatic section. Either way, find out the client_id.
2. Is this a subsequent drop to a group targeted before? For example, on a monthly basis we were sending messages to Bosley campaigns labeled "Male Inquiry DM." They were different groups, but I considered them the same campaign. However, the Response Drops have all been different campaigns. At any rate, new campaigns are set up in the table called outbound_campaigns.
    1. Client_id = you got this.
    2. Name = [Sometimes the conceptual name ("Female Procedure"), sometimes named like inbound campaigns ("Scott Yancey: Yancey to 72346")]
    3. Created = [current timestamp]
    4. Launch = [Date initial messages sent]
    5. Active = 1

    Add the id from this table to the campaign setup spreadsheet. This will be known as the "Stiki_campaignID"

3. Update outbound_summary table.
    6. groupNumber = [if a subsequent drop to an already created campaign (i.e. they have different Skycore campaign IDs, but really they're the same stiki campaign, put that number here]   If groupNumber > 1, best practice is to set endTime on the previous group for the same campaign to equal start time of new group.
    7. Dnis = tracking phone number, to be used to link with the calls table.
    8. sc_campaignID = [should be on the setup spreadsheet. Can ask Jessica, or can also pull from Skycore]
    9. stiki_campaignID = [added to the setup spreasheet above]
    10. otherQualifier = [blank by default, used for A/B testing]
    11. sendTime = [UTC time of drop. Reported and also used to give start and end times to dnis associations]
    12. endTime = '2099-12-31'
    13. quantitySupplied = 0 (Jessica will provide numbers when the lists are uploaded, update this then)
    14. billingMmsid = 0
4. Update filters. If this is a previous client,  shouldn't need to do anything. Otherwise, the client will have the default values for the filters, and the defaults aren't right for outbound. Outbound clients need filters with the following names:
    15. Stiki_Outbound_Clients = [value = the client id, visible = 0, group = ''] Note that this is different from the inbound clients filter which uses the name as the filter value.
    16. Stiki_Outbound_Campaigns = [value = '', visible = 1, group = client_id] This is another child filter, so needs the value of the parent filter (the client_id) given as the group.
    17. Outbound_Group_Number = [value = '', visible = 1, group = '']


## UPDATING THE DASHBOARD(S)

There will probably be data from the new campaign already in the system. We just need to make sure it's formatting properly now that the campaign is set up in the database.



1. Refresh filters. Go to the dashboard viewed by the client (default is AWS Stiki Reporting - Main Client Dashboard). Turn the "Show_tests" filter to 'On'. Then click the name "Stiki_Clients." (if a new campaign was made; if not, go directly to client_campaigns). This brings up an editing pane. At the top is a refresh button. Click that. Wait until it completes, and then repeat for "Client_Campaigns." Alternatively, refresh Stiki_Outbound_Clients and then Stiki_Outbound_Campaigns similarly.
2. Filter the dashboard so that only the relevant client and campaign(s) appear. Edit each of the graphs to force the new element to be a bar instead of a line.
3. (optional) Go to the main client dash and repeat the graph updating. You won't need to refresh the filters again.


## CREATING A LOGIN

There's not a whole lot tricky about this, but here's my process.



1. Go to a text editor. I use an online one because I like the screenshot plugin better than the built in Mac screenshot. Personal choice. Make up your username and password at this point. Sandy likes the company name as the username, and something like username##[symbol] as the password. But there's no strict formula. I like to tell myself that this makes it more secure ;-). Take a picture and save it somewhere, but leave your text editor open so that you can copy/paste the password.
2. Login to reports.getstiki.com
3. Click the Admin Panel in the upper right.
4. Go to User management -> Create a user
5. Enter the username and password you made up in step one. Don't make anyone an admin.
6. Logout of the reporting portal, and then log back in as the user you just created. Make sure the right data is showing (you've just seen the relevant data when updating the graphs through Periscope).
7. Send the image to Sandy and (Jessica? Balie?), letting them know that [client] can now log into the reporting portal using the following credentials: [picture]


## OTHER DASHBOARDS

We have duplicates of all dashboards in the system referring to Rackspace. You're interested in the dashboards whose names start with AWS. There's a testing dashboard. Because these dashboards could theoretically be used all the time, update the main client dash with great caution, only after testing by copying the chart to the testing dashboard, then messing with it, then moving it back or copy/pasting the updated SQL.


## SUB-LOGIN FOR MEDIA

Dashboards and filters are managed on a per-client basis. Some of our clients, however, are media companies and have their own clients. They occasionally want to provide a login to their clients which has a view into a subsection of data. We manage this by creating a dummy client. You can create this client using the Automatic process described above, and then editing the filters table.  The Stiki_Clients filter should have the value of the media company and not be visible, and there will need to be multiple entries for the Client_Campaigns filter, one for each of the campaigns that the new user will be able to view. The group for each of these should be the media company.


```
For example:
Hybrid Media (client id 87)  has multiple campaigns with us for several of their clients. Their login shows them data for all of these campaigns. They requested a login for their 'ZQuiet' client, so that that client could view just their own data.
I created a new client called 'Hybrid ZQuiet' that was a copy of the Hybrid client (client id 95).
Then I went to the filters table and updated the Stiki_Client filter for client id 95, replacing the name 'Hybrid ZQuiet' with 'Hybrid Media.'
I deleted the Client_Campaigns filter. Then I inserted several new ones by running the query: INSERT INTO filters (client_id, name, value, visible, `group`)  SELECT 95, 'Client_Campaigns', campaigns.name, 0, 'Hybrid Media' FROM campaigns WHERE campaigns.name LIKE "ZQuiet%" This enables them to see the data for each of these campaigns, but never gives them a view into the Client_Campaigns filter, which would show all of Hybrid's data.

```



<!-- Footnotes themselves at the bottom. -->
### Notes

[^1]:

     If a special dashboard was created for this client, or they have a special situation like call data, that dashboard_id should be used in place of the 200211 above. 200211 is the AWS Stiki - Main Client Dash in Periscope. Default given in the portal 79749 is the Rackspace Main Client Dash.

[^2]:

     See previous note.


<!-- Docs to Markdown version 1.0β16 -->
