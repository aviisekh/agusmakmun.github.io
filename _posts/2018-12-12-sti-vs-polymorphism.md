---
layout: post
title:  "STI vs Polymorphism"
date:   2018-12-12 17:17:10 +0545
categories: [ruby]
---


STI refers to single table inheritance. Though polymorphism and STI sounds similar but there is a huge difference. Polymorphism is idea that an object or a record can exist in multiple forms. For example say we have a images table where we store the information of the image. The image can sometime exist as an image for the user, sometime can exist as an image of the product of the user, sometimes can exist as an image of the company of the user and so on. All the images however shows the same behaviour, meaning all the images have similar attributes or methods. 

In contrast STI is the concept where the records are stored in the single table, however all the objects in the same table need not to be similar, or need not to show the same attributes. And so different types of object are referenced with different classes and their custom attributes are defined inside their corresponding classes. 
For example, say animal is the table we are using. Dogs and people are the animal used in our application. They both inherit from the base class animal. However Dog class can have seperate set of actions, or methods. Similarly People class have separate set of actions defined inside the class. This seperates out the responsibility of each class although they both resides in same table. 