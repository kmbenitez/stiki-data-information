# Reporting Overview

## Overview

Setup is different for all campaign types, but all can be thought of consisting of three basic steps: setting up a new campaign in the database, updating the dashboards, and creating a login to the reporting portal.


## Inbound


### Setting Up A New Campaign In The Database

Amy typically sends a dashboard creation/update request enumerating the client name, the launch date, the product name, and the associated campaign or campaigns. You'll also need the Skycore campaign ids, either from Skycore, or from whomever set up the campaign.


#### Automatic

1. Login to [reports.getstiki.com](reports.getstiki.com). Open the Admin Panel from the links on the upper right in green.

2. Is setup for a current client? If not, create a new client (Client Management -> Create Client)
    * Client name as given by Amy.
    * Dashboard ID = 200211 (see [note on dashboards](#differentdashboards))
    * Default Daterange = 30
    * Daterange Visible checked.
    * Overriding URL blank.

    Click 'Create Client' and look for success or failure message. If the client created is differently named than you wanted (sometimes happens with special characters), note it so that you can change it later. To do so, see the [manual instructions](#manual).

> *Different dashboards* <a name="differentdashboards"></a>
>
> If a special dashboard was created for this client, or they have a special situation like call data, that dashboard_id should be used in place of the 200211 above. 200211 is the `Keyword - Main Client Dashboard` in Periscope.

3. Create campaigns (Campaign Management -> Create Campaign)
    * ID (Skycore) = The Skycore campaign id
    * Campaign Name = ProductName: Keyword to Shortcode (eg ZQuiet: Bed to 246810)
    * Client Name = provided by Amy, choose from dropdown menu
    * Launch Date = provided by Amy

    Click 'Create Campaign' and look for success or failure message. Again, if the campaign name is not what you wanted, then refer to the [manual instructions](#manual).


#### Manual<a name="manual"></a>

You may need the manual instructions if there are a bunch of campaigns to set up at once and you'd rather just use SQL, if there are special characters in the client or campaign name, or for other situations like the media companies (Barrington, Hybrid), who occasionally want logins for their clients. The setup information required is mentioned in the description of the 'Automatic' setup, but includes: Client name, Campaign id, Launch Date, Product name, shortcode and keyword. Work is done in the `getstiki` database on AWS.

1. Is it for a current client? If so, note the client id. If not, create a new client in the clients table of the database. Some default values:
    * Order_notification = 0
    * Active = 1
    * Dashboard_id = 200211 (see [note on dashboards](#differentdashboards))
    * Url = 0
    * Created = current timestamp
    * Default_daterange = 0
    * Daterange_visible = 1
    * Aggregation = (empty string)
    * Aggregation_visible = 0

    Note the id of the newly created client.

2. Create a campaign in the campaigns table of the database. It should have the client_id  of the client created/noted above. Other values:
    * shortlink_id = optional
    * Mc_campaign_id = the Skycore campaign id
    * Sc_campaign_id = the Skycore campaign id
    * Name = ProductName: Keyword to Shortcode
    * Created = current timestamp
    * Order_notification = 0
    * Active = 1
    * Dest_number = shortcode
    * Keyword = keyword
    * participTVSquared = 0
    * tvs_collectorId = 0
    * tvs_siteId = 0

For instance, I inserted 5 new campaigns using the SQL
```
INSERT INTO campaigns(name, mc_campaign_id, sc_campaign_id, client_id,Active,launch,created) VALUES
('GenStone: House to 246810',9630,9630,18,1,'2019-03-11',now()),
('GenStone: Fix to 246810',9633,9633,18,1,'2019-03-11',now()),
('GenStone: Stone to 246810',9634,9634,18,1,'2019-03-11',now()),
('GenStone: Rock to 246810',9635,9635,18,1,'2019-03-11',now()),
('GenStone: Improve to 246810',9636,9636,18,1,'2019-03-11',now());
```
If you don't have a preferred tool to get from the setup summary to this SQL, you can learn about [Sublime Text]('Tools.md')

3. Add the correct filters to the filters table.  - Filters are automatically created when a client is created, and are client specific. In the default case, this works fine. However, it is helpful to understand the structure of the table.
    * Client_id = the id of the client associated with the user viewing the dashboard.
    * Name = the name of the filter
    * Value = the value provided to Periscope to use the filter
    * Visible = whether the filter should be made visible to the client and its user(s)
    * Group = required for child filters, this gives the parent value.

    For inbound campaigns, we have Stiki_Clients filters (value is the client name), which should not be visible, and Client_Campaigns filters (value usually blank), which should be. Client_Campaigns is a child filter, so it needs to know what the parent value is, so the client name is specified in the group field. These are visible because clients can pick and choose which of their campaigns to view, but client is not so that they can't view the data that belongs to other clients.

### Other campaign components

#### Calls

If a client wants to track calls for their campaigns, we will set up a phone number (also sometimes called a DNIS) to forward to their call center (currently we use CallRail for this). This setup is done in the `dniss` table.
* dnis = 10 digit phone number supplied by CallRail
* client_id = which client this phone number is being billed to
* mc_campaign_id = the Skycore campaign id that this phone numer should be tracked for
* start = the date at which phone calls to this number should start being attributed to this campaign. This exists in case clients want to switch a phone number from one campaign to another
* end = the date at which phone calls to this number should stop being attributed to this campaign. In general, value should be far in the future.
* reportingCategory = helps decide where data about this phone number should appear. Current values can be 0 (general campaign usage), or 1 (billable autoresponders)

#### Billable Autoresponders

The general Stiki package for inbound/keyword campaigns includes two MMS autoresponders, generally sent immediately upon joining a campaign, and 3-5 minutes later. However, sometimes clients change this standard. If there are more than two MMS autoresponders, or if they are sent more than 24 hours after the subscription, they are not considered part of the Stiki package, and are billed separately. Information about these messages is contained in the `billableAutResponders` table.
* sc_campaign_id = the Skycore campaign that this billable autoresponder belongs to
* mmsid = the Skycore MMS ID being sent as a billable autoresponder
* smsBody = could be used if we got SMS bodies in our outgoing message postbacks. Not used
* shortlink_id = the link id of the shortlink sent in this MMS. This is used so we can report on click activity just for the billable autoresponder to help clients justify paying for them ;-)
* dnis_id = similar to shortlink_id, but for calls rather than clicks.

Something important to know is that in addition to having the link id stored here, the reportingCategory for the link or dnis should be set to 1. This will prevent it from clouding up the click/call data. There is a chart on the 'Stiki Systems Monitoring' dashboard on Periscope that will show any links that have been named here, but the reporting Category has not been set correctly. [Read more.]('DashboardsSummary.md')


## Outbound

There is no automatic option for creating outbound campaigns in the database. That's okay for now, because it's actually easier to use the campaign setup spreadsheet that Amy and Jessica create and do it manually, to save a bunch of repetition.


### Semi-automated Workflow For Response
Examples of completed files are available on Trello. See [week 13](https://trello.com/c/8Msqgjnl).
1. Jessica adds a summary Excel file to Trello with information, including a "cover sheet" with just the summary information needed: the sc_campaign_id, keyword, shortlink ID 1, shortlink ID 2, and mmsid1 (in the example file, the first sheet)
2. Add the date sent to the right of the block of information. On week 13, this is in column 'I', and the formula for the group 1 sends is `=IF(RIGHT(E2,1)="B","2019-03-14","2019-03-20")`. Make sure to update the dates appropriately. Group 2 will all be sent on one (different) day, so input that manually. A single quote before the date prevents Excel from interpreting the date strangely.
3. The column on the far left can be calculated for group 1 now. This formula will add the campaign, and the group one summary. Once again, you'll need to update a date in the formula, in this case, the launch date. The launch date determines the order of the campaigns in the Periscope filter, so just make sure it's greater than the previous week. On week 13, the formula was ```=CONCATENATE("Insert into outbound_campaigns (client_id, name, created, launch, Active) VALUES (82, """,B2,": ",PROPER(E2)," to 72346"", now(), '2019-03-13', 1);insert into outbound_summary (groupNumber, sc_campaignId, stiki_campaignID, sendTime, endTime,mms1_shortlinkID, mms2_shortlinkID,billingMmsid,client_campaign_identifier) VALUES (1, ",C2,",last_insert_id(),'",I2," 20:00:00', '2099-12-31 00:00:00',",F2,",",G2,",",H2,",'",SUBSTITUTE(D2,"&lc=","-"),"');")```
4. Copy that whole section and run it against the database.
5. Determine which Stiki campaigns the group 2 sends should be connected to. I run a query like `SELECT * FROM `outbound_campaigns` WHERE name like '%1319%B to 72346'`, updated to reflect the week number. Record these campaigns to the right of the date column (column 'J').
6. On the far left, use the following formula for the group 2 sends ```=CONCATENATE("insert into outbound_summary (groupNumber, sc_campaignId, stiki_campaignID, sendTime, endTime,mms1_shortlinkID, mms2_shortlinkID,billingMmsid,client_campaign_identifier) VALUES (2, ",C12,",",J12,",'",I12," 20:00:00', '2099-12-31 00:00:00',",F12,",",G12,",",H12,",'",SUBSTITUTE(D12,"&lc=","-"),"');")```. Make sure the calculation pulls from the correct row).
7. Run those queries against the database.


### Otherwise

1. Is this for a current client? If not, create the client using the steps described above in the manual or automatic section. Either way, find out the client_id.
2. Is this a subsequent drop to a group targeted before? For example, on a monthly basis we were sending messages to Bosley campaigns labeled "Male Inquiry DM." They were different groups, but I considered them the same campaign. However, the Response Drops have all been different campaigns. At any rate, new campaigns are set up in the table called outbound_campaigns.
    * Client_id = you got this.
    * Name = [Sometimes the conceptual name ("Female Procedure"), sometimes named like inbound campaigns ("Scott Yancey: Yancey to 72346")]
    * Created = [current timestamp]
    * Launch = [Date initial messages sent]
    * Active = 1

    Add the id from this table to the campaign setup spreadsheet. This will be known as the "Stiki_campaignID"

3. Update outbound_summary table.
    * groupNumber = [if a subsequent drop to an already created campaign (i.e. they have different Skycore campaign IDs, but really they're the same stiki campaign, put that number here]   If groupNumber > 1, best practice is to set endTime on the previous group for the same campaign to equal start time of new group.
    * Dnis = tracking phone number, to be used to link with the calls table.
    * sc_campaignID = [should be on the setup spreadsheet. Can ask Jessica, or can also pull from Skycore]
    * stiki_campaignID = [added to the setup spreasheet above]
    * otherQualifier = [blank by default, used for A/B testing]
    * sendTime = [UTC time of drop. Reported and also used to give start and end times to dnis associations]
    * endTime = '2099-12-31'
    * quantitySupplied = 0 (Jessica will provide numbers when the lists are uploaded, update this then)
    * billingMmsid = 0
4. Update filters. If this is a previous client,  shouldn't need to do anything. Otherwise, the client will have the default values for the filters, and the defaults aren't right for outbound. Outbound clients need filters with the following names:
    * Stiki_Outbound_Clients = [value = the client id, visible = 0, group = ''] Note that this is different from the inbound clients filter which uses the name as the filter value.
    * Stiki_Outbound_Campaigns = [value = '', visible = 1, group = client_id] This is another child filter, so needs the value of the parent filter (the client_id) given as the group.
    * Outbound_Group_Number = [value = '', visible = 1, group = '']

## Flow campaigns
There is no automated process for flow campaigns, but there's also not much to do, either. Flow campaign information is contained in the `flow_campaigns` table of the database.
* client_id = you're here
* platform_campaign_id = The Skycore campaign id, future-proofed a bit
* platform = "SKYC," unless platform has changed
* product = The name of the product, usually used as the first part of the campaign name for an inbound campaign. For Response, this corresponds to personality.
* name = campaign name
* launch = datetime when texts should start being counted
* created = timestamp of insertion

## API campaigns

There is no automated process for API campaigns.

### New API user

    1. Create a record in the `api_users` table.
        * name - this will be the value specified in the `group` parameter of their API requests
        * secret - leave blank for now. Will be a hashed value of their API key
        * client_id - You got it.
        * stikiSendAuthorized - Have they signed the paperwork, and paid the fee to have creative control? Boolean value
        * created - timestamp for creation
        * lastLogin - broken
    2. Create an API key in AWS. Enable the key on the Demo usage plan. This will allow them access to the demo endpoint, which has a restricted quota and sending limitations.
    3. Update `secret` in `api_users` with something like the following

```python
import bcrypt

key = **HERE**
username = **HERE**

secret = bcrypt.hashpw(key.encode(), bcrypt.gensalt())

if not bcrypt.checkpw(key.encode(),secret):
    print("They don't match!!!")
    exit()

try:
    writeconn = pymysql.connect(os.environ["db_host_write"],
                        user=os.environ["db_user"],
                        passwd=os.environ["db_password"],
                        db=os.environ["db_name"],
                        connect_timeout=5,
                        charset='utf8mb4')
except pymysql.OperationalError as e:
    logger.error("ERROR: Unexpected error: Could not connect to MySql instance:" + str(e.args[0]) + " "+ e.args[1])
    raise(e)
    sys.exit()

with writeconn.cursor(pymysql.cursors.DictCursor) as cur:

    try:
        cur.execute("UPDATE api_users set secret = %s WHERE api_users.name = %s",
                    (secret,
                    username))
        print(cur._last_executed)
    except Exception as e:
        raise Exception('could not access database')
    if cur.rowcount == 1:
        writeconn.commit()
```

### Campaign record

Stored in `api_campaigns`.
1. api_user_id = The API key holding user who will be using this campaign. Note that API users are distinct from general users of the system
2. platform_campaign_id = The Skycore campaign id future proofed a bit
3. platform = "SKYC" unless Stiki has moved on
4. shortcode = The shortcode this campaign can send from. Confirmed by request in send message. Necessary to use the correct Skycore API key
5. product = The name of the product being advertised.
6. name = The campaign name to be displayed in reporting
7. launch = The date after which texts should start being counted as real, rather than tests.
8. created = Timestamp for creation of the record

### Templates?
If this is a Stiki Notify campaign, templates will need to be created. See the documentation for the Notify API.

## Updating The Dashboard(s)

There will probably be data from the new campaign already in the system. We just need to make sure it's formatting properly now that the campaign is set up in the database.

1. Figure out what dashboards need to be updated. If you used the automated process for an inbound campaign, it will have told you which periscope dashes are seen by users belonging to the client you specified for the campaign. In most cases, that's all you need. In the cases of manual setup, there is a Periscope chart called 'what periscope dashboard(s) does this client see' that will give you similar information.
1. Refresh filters. Go to the dashboard viewed by the client (default is AWS Stiki Reporting - Main Client Dashboard). Turn the "Show_tests" filter to 'On'. Then click the name "Stiki_Clients." (if a new campaign was made; if not, go directly to client_campaigns). This brings up an editing pane. At the top is a refresh button. Click that. Wait until it completes, and then repeat for "Client_Campaigns." Alternatively, refresh Stiki_Outbound_Clients and then Stiki_Outbound_Campaigns similarly.
2. Filter the dashboard so that only the relevant client and campaign(s) appear. Edit each of the graphs to force the new element to be a bar instead of a line.
3. (optional) Go to the main client dash and repeat the graph updating. You won't need to refresh the filters again.


## Creating A Login

There's not a whole lot tricky about this, but here's my process.



1. Go to a text editor. I use an online one because I like the screenshot plugin better than the built in Mac screenshot. Personal choice. Make up your username and password at this point. Sandy likes the company name as the username, and something like username##[symbol] as the password. But there's no strict formula. I like to tell myself that this makes it more secure ;-). Take a picture and save it somewhere, but leave your text editor open so that you can copy/paste the password.
2. Login to reports.getstiki.com
3. Click the Admin Panel in the upper right.
4. Go to User management -> Create a user
5. Enter the username and password you made up in step one. Don't make anyone an admin.
6. Logout of the reporting portal, and then log back in as the user you just created. Make sure the right data is showing (you've just seen the relevant data when updating the graphs through Periscope).
7. Send the image to Sandy and (Jessica? Balie?), letting them know that [client] can now log into the reporting portal using the following credentials: [picture]


## Adding dashboards to a login
The reporting portal enables users to see one or more dashboards. Information about the dashboards is drawn from tables on the `getstiki` database
    - `users` -- contains information about the user, including password
    - `dashboards` -- dashboards belong to a client, and information about them includes the Periscope ID, whether the daterange and/or aggregation filter should be visible, and what the daterange should start at. Unless directed otherwise, this should be 30.
    - `filters` -- describe the filters that should be applied to a particular Periscope dashboard before rendering it to the user. In general, we filter by client, and make that vilter invisible, and include the filter for campaigns, which is visible. The campaigns filter is a child of the client filter, meaning what filter options it displays depends on what parent value is selected. Therefore, each filter by campaign must include the information about the client in the field called 'group.' For Inbound/Keyword campaigns, the filter values are the client name, whereas for other campaign types, the filter values are the client name.
    - `userDashboards` -- what dashboards a user should see, and in what order.

So, to add a dashboard to a user's view, first find or create a record of that dashboard for the client. Add the needed filters to the filters table (usually copying existing setups is best). Then add a userDashboard, and decide on the order.


## Sub-login For Media

Dashboards and filters are managed on a per-client basis. Some of our clients, however, are media companies and have their own clients. They occasionally want to provide a login to their clients which has a view into a subsection of data. We manage this by creating a dummy client. You can create this client using the Automatic process described above, and then editing the filters table.  The Stiki_Clients filter should have the value of the media company and not be visible, and there will need to be multiple entries for the Client_Campaigns filter, one for each of the campaigns that the new user will be able to view. The group for each of these should be the media company.


> Hybrid Media (client id 87)  has multiple campaigns with us for several of their clients. Their login shows them data for all of these campaigns. They requested a login for their 'ZQuiet' client, so that that client could view just their own data.
> I created a new client called 'Hybrid ZQuiet' that was a copy of the Hybrid client (client id 95).
> Then I went to the filters table and updated the Stiki_Client filter for client id 95, replacing the name 'Hybrid ZQuiet' with 'Hybrid Media.'
> I deleted the Client_Campaigns filter. Then I inserted several new ones by running the query: INSERT INTO filters (client_id, name, value, visible, `group`)  SELECT 95, 'Client_Campaigns', campaigns.name, 0, 'Hybrid Media' FROM campaigns WHERE campaigns.name LIKE "ZQuiet%" This enables them to see the data for each of these campaigns, but never gives them a view into the Client_Campaigns filter, which would show all of Hybrid's data.

## Alternative structure for media company reporting
We have taken recently to creating a new campaign for the clients of the media company. Campaigns are assigned to that client instead. To enable the media client to see the results as well, we add another filter for the media client view of the dashboard, with the new client listed as the value. This filter should still not be visible. The biggest challenge to this approach is remembering that the media company should receive the bill, rather than the listed client.