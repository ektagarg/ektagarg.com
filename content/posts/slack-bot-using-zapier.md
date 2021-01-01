---
title: "Slack bot using [Zapier + Redash + Google sheets + Slack]"
date: 2021-01-01T09:01:58+05:30
description: "Let's create a slack bot to get daily stats for your website."
tags: [Slack bots, Zapier, Automation]
categories: Automation
comment : true
---

__What are we going to do?__

Creating a slack bot that will post a message on a specific channel at a certain time 
(say, 9 am) about the total number of orders placed on your website in the last 24 hours.

## Assumptions 
- Using slack for communication, organization of your work.
- Know how to write DB queries, I will be using [redash](https://redash.io/) for writing queries. 
- Know the basics of google sheets.

## Tools and terms

These are some terms we will be using while creating this slackbot. Please read them if you have never used zapier earlier.

### Zapier
[Zapier](https://zapier.com/) simply helps you save your time with automation. You can connect multiple apps to automate repetitive/boring tasks without coding or relying on developers to build integration. 

### Zap
A Zap is an automated workflow that connects your apps and services. You can create a zap that will fetch some data and then trigger events based on conditional actions you have specified. When you turn your Zap on, it will run the action steps every time the trigger event occurs.

### Trigger
A trigger is an event that starts a Zap. For example, in this case, where we are creating a slack bot, each time when there is new data from the query, you will receive a message on slack about the updated data.

### Redash
[Redash](https://redash.io/) helps you write queries to your data source, can schedule your queries (fetch at a particular time of the day, week), create dashboards, visualizations, etc.

## Steps to follow
1. Create a query on redash which will give you a total number of orders placed in the last 24 hours.

![Query result](/images/slack-Bot/query.png)

2. I am setting a refresh schedule of every day at 08:45 am. Because I need this total order stats update once a day in the morning.  

![Query result](/images/slack-Bot/refresh-schedule.png)

3. Create a google sheet that will be fetching data from this redash query. We will be using IMPORTDATA() function which allows you to quickly import the data from the URL of a CSV.

```
IMPORTDATA("https://reports.xyz.io/api/queries/13292/results.csv?api_key=nlnOwQqweKZGsTIk7oE6ErVRaqK3D8weVdaqkcO5C
")
```

Your sheet will look like this:
![Query result](/images/slack-Bot/sheet.png)

4. Next, set up an account on [zapier](https://zapier.com). Now, we have to create a zap using Google sheets + Slack. Because we want to trigger an event which is a slack message whenever there is any change in the spreadsheet.

5. Select Google sheets as your app in `Trigger`, add a trigger event which can be a New spreadsheet, New worksheet, New spreadsheet row (depending on the data updates in your sheet).

6. Connect your google account which has this daily updates sheet and set up a trigger by selecting a spreadsheet, worksheet, a trigger column. Click on the test trigger to load the latest data present in your sheet. This will look like this.

![Query result](/images/slack-Bot/zap.png)

7. After setting the Google sheet, we have to send a message on slack that we got new data in the sheet. Similar to Google sheets, Choose app and event (slack), choose your slack account, and set up actions like this:

![Query result](/images/slack-Bot/slack-actions.png)

8. You are all set up after adding all the actions, Now we are good to test the action. Before clicking on Test and continue, Verify all the options, data, and format. 
Let's click test and continue and check on slack if we got the message. Yes, here is the message, I got: 

![slack message](/images/slack-Bot/slack-msg.png)

That's it!! You just created a slack bot that will take care of updating you every day about the total number of orders on your website in a day. 
Similarly, you can automate a lot of your boring stuff using zapier.

