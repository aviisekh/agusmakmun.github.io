---
layout: post
title:  "Database and Time Zone"
date:   2019-02-05 21:14:10 +0545
categories: [ruby]
---


1. There is specified time zone of the database. 
2. The timestamps are saved in utc in the database. However if we define the  default timezone for the application, timevalues are accessed under the specified timezone.
3. While writing the time as well, if the timezone of app is defined, the time in that time zone is converted into utc and written in the database.
4. If we are querying the records withtin some time range via third party applications or a raw sql qyery, we need to convert the time into utc and the run the query. 