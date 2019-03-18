---
layout: post
title:  "Finite State Machine Over Enum"
date:   2019-02-02 17:17:10 +0545
categories: [ruby]
---


1. The flow is more secure. No validations needed. 
For example: The completed state cannot be changed to the start state.

2. We can define the callbacks during the transition of the state. Like notifying when the task is completed.

3. Maintaining the code is easy. For example comparing the states, identifying the vaild states. 

4. Change in the states, e.g: removing some intermediate state using the enum might bring the huge code refactor. State machines help to reduce such problems.

5. The values in the database column are more readable. So no mapping is needed if some other system reads the data.

