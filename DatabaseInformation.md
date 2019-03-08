<!----- Conversion time: 1.15 seconds.


Using this Markdown file:

1. Cut and paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β16
* Fri Mar 08 2019 08:36:34 GMT-0800 (PST)
* Source doc: https://docs.google.com/a/getstiki.com/open?id=1_qvIUOUv_YxYqnngfsDl7h__zzczFHFVgLNEyXmtU2o
* This document has images: check for >>>>>  gd2md-html alert:  inline image link in generated source and store images to your server.


----->


<p style="color: red; font-weight: bold">>>>>>  gd2md-html alert:  ERRORs: 0; WARNINGs: 1; ALERTS: 1.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>>  gd2md-html alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p><a href="#gdcalert1">alert1</a>

<p style="color: red; font-weight: bold">>>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>




<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Stiki-Database0.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/Stiki-Database0.png "image_tooltip")


**GetStiki**


## Database Information


### What information drives what functionality?


### Updated: September 28, 2018


## Dashboards



1. A user (table: **getstiki.users**) has one or more dashboards.
2. A dashboard (table: **getstiki.dashboards**) belongs to a client, has a Periscope ID, and some settings related to Periscope. It also contains a URL override field.
3. Filters (table: **getstiki.filters**) are applied to the dashboard when there is not a urloverride. These filters are generated automatically upon creation of a new client, but they are created for a single inbound dashboard. Anything else requires a manual update.
4. The relationship between the two is described in **getstiki.userDashboards**. This also defines the ordering (left to right on the user's page) of the dashboards.


## Shortlinks



1. Shortlinks (table: **shortlink.links**) may have a campaign id, but must have a sourceDomain, a sourceLink, and a destination ('http://' required).
2. Shortlinks that shouldn't be counted in general reporting (right now, just links in billable Autoresponders, where reporting category is 1) have a reportingCategory that is not zero.


### 	Personalized Destinations


    Shortlinks with personalized destinations have a dest that contains a fieldName surrounded by double curly braces. This could be:



    1. [http://{{customEntireURL](http://{{customEntireURL)}}
    2. [http://google.com/myName={{myName](http://google.com/myName={{myName)}}
    3. http://responseEvent.com/event=209834o9328ru&lc=abunchofstatic-thing-and-{{oneCustomBit}}-more-things

	In any event, the shortlink system will receive a request for text2offer.com/keyword/trackingID and will look up the trackingID in **getstiki.customerInfo**


## Reply Tool

Replies are defined (table: **getstiki.replies**) on a campaign or client basis.

Replies are registered in the **getstiki.incoming_message** field reply_id

The reply tool validates that the message you're replying to belongs to a campaign that belongs to the same client as the user attempting to reply.


## Custom campaigns

Our integration with Skycore reposts incoming messages from campaigns set up in **getstiki.custom_campaigns** to the destination set there. The format of this repost is expected by the following features.


### Unmonitored message autoresponder


    The campaign of the message is used to look up the appropriate response in **getstiki.custom_responses**. Provided the incoming message is not a stop response, the response found there will be sent as a response to the incoming message. The message is logged in **getstiki.custom_responses_log**


### ExactTarget Posting


    The campaign of the message is used to look up possible tags in **Response.custom_exactTargetTags**. Provided the incoming message body matches the regular expression for the tag, the matching tag name and value will be sent to Response's ExactTarget system. The post is logged in **Response.custom_exactTargetLog**


## iCal links


## Reporting


##
    Billable Autoresponders


##
    Outbound


##
    Inbound


##
    API


## Data formats

Phone number - 10 digits


<!-- Docs to Markdown version 1.0β16 -->
