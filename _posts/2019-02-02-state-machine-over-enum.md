---
layout: post
title:  "Finite State Machine Over Enum"
date:   2019-02-02 17:17:10 +0545
categories: [ruby]
---


1. The flow is more secure. No validations needed. 
For example: The completed state cannot be changed to the start state.

2. We can define the callbacks during the transition of the state. Like notifying when the task is completed.