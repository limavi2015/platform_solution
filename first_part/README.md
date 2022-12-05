# Part 1 - Algorithm

## Background information for this exercise:
Assume that the channel engagement platform is a mobile or tablet application. 
- Flyers have unique identifiers 
- Merchants have unique identifiers
- Merchants have multiple flyers

You have been given a sample dataset with the following schema: [timestamp, user_id, event, flyer_id, merchant_id]. 

There are a number of events which the mobile app collects as the shoppers the app. These are the 'event" types in the preceding schema.
- flyer_open - Whenever a flyer is opened by a user, to see the flyer contents 
- item_open  - Whenever an item is opened by the user, to see the item details 
- list_flyers  - Whenever a listing of the flyers is shown 
- shopping_list_open - Whenever a user opens their shopping list 
- favorite - Whenever a user adds a merchant to their favourites.
   
   
Your task is to:
1. Compute the average time on flyer per user.
2. Next, generate an output that will back a Business Intelligence (Bl) report that will be shared with our merchant partners.
3. Explain how your algorithm scales for:
   - 1 Million Events (~10 MB of data) 
   - 1 Trillion Events (~10 TB of data)
To write your algorithm, you may use any of: Python, Java.

# Solution

I was asked to Compute the average time on flyer per user however, with the events that the mobile app collects at this moment we cannot generate the metric requested. 
Then I decided to generate another metric about the number of open flyers per day. In this file [script.ipynb](https://github.com/limavi2015/platform_solution/blob/main/first_part/script.ipynb) is the code that I use to generate that metric. 
This metric was generated with both Pyspark and Python scripts using generators.
In order to generate the average time on flyer per user it is needed to know the complete event flow of the user, to validate when an open flyer is considered closed.

My algorithm scales from 1 million Events to 1 Trillion events because I can use Spark partitions that allow us to process the data in parallel, then we only need to have the resources to scale horizontally.


