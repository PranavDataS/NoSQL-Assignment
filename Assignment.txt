>>Student Name: Pranav Shah
>>Batch - BDAP PT Batch 01

>>Assignment Subject: MongoDB - Twitter Data

----------------------------------------------------------------------------
>>1. [4 marks] Write and submit code to load all tweets from the JSON file and store them in your local MongoDB server.

>>File name : "twitter.json"
>>Steps  - Unzip the file in the linux enviornment and copy the JSON file in the windows local folder
>>Importing data in the Test DB which is the default DB

>>Command for Import in the command prompt 
mongoimport --db <Database> --collection <Collection name> --drop --file <filePath>/twitter.json
>>My Query
mongoimport --db  test --collection twitterdata < "F:\twitter.json"

>>To check if data is loaded - 
show dbs
use test
db.twitterdata.count()
db.twitterdata.find().limit(10).toArray()

>>Conclusion / Result:
>> 308930 documents uploaded into the twitterdata

----------------------------------------------------------------------------
2. [4 marks] What is the 5th most frequent hashtag in the dataset? Submit along with code. (the answer should be derived using a mongodb query)

Query :

db.twitterdata.aggregate
    ([{$unwind: '$entities.hashtags'},
      { $group: {_id: '$entities.hashtags.text', tagCount: {$sum: 1} }}, 
      { $sort: { tagCount: -1 }},{$skip:4}, { $limit: 1 }
    ])	
		
Conclusion / Result:
{
	"result" : [
		{
			"_id" : "analytics",
			"tagCount" : 15567
		}
	],
	"ok" : 1
}

----------------------------------------------------------------------------
3. [4 marks] What is the 7th most frequent @-mention in the dataset? Submit along with code. (the answer should be derived using a mongodb query)

Query:
db.twitterdata.aggregate([
                          {$unwind: '$entities.user_mentions'}, 
                          { $group: {_id: {"@Mention":'$entities.user_mentions.screen_name'}, tagCount: {$sum: 1} }}, 
                          { $sort: { tagCount: -1 }},{$skip:6}, { $limit: 1 }
                         ])	

Conclusion / Result:
{ 
    "_id" : {
        "@Mention" : "Forbes"
    }, 
    "tagCount" : NumberInt(2711)
}

----------------------------------------------------------------------------
4. [4 marks] Which twitter user has 3rd most number of followers? Submit along with code. (the answer should be derived using a mongodb query)

Query :
db.twitterdata.find(
                    {"user.followers_count":{$ne:null,$gt:0}},
                     {"_id":0,"user.followers_count":1,"user.name":1,"user.screen_name":1}
                    )
                     .sort({"user.followers_count":-1}).limit(1).skip(2)

>> We can do the same without filtering the data for Followers count - To find the 3rd we skip 2 and limit as 1
db.twitterdata.find({},
                    {"_id":0,"user.followers_count":1,"user.name":1,"user.screen_name":1}
                    )
                    .sort({"user.followers_count":-1}).limit(1).skip(2)

Conclusion / Result:
{ "user" : { "name" : "TIME.com", "screen_name" : "TIME", "followers_count" : 9714612 } }

----------------------------------------------------------------------------			
5. [4 marks] Which twitter user's tweets were retweeted 8th most number of times? Submit along with code. (the answer should be derived using a mongodb query)

Query :

db.twitterdata.aggregate([
                         { "$group": {"_id": {"user.name":"$retweeted_status.user.name","Text": "$retweeted_status.text"}, 
                           tagCount: { $sum: "$retweeted_status.retweet_count" }}},
                           { $sort: { tagCount: -1 }},{$skip:7},{ $limit: 1 }
                         ])

Conclusion / Result:

{ 
    "_id" : {
        "user.name" : "Majalah InfoKomputer", 
        "Text" : "Manakah solusi Big Data Unstructured Analytics?A.NetApp Solutions for Oracle B. Solutions for SAP C.FlexPod Select with Hadoop #NetAppQuiz"
    }, 
    "tagCount" : NumberInt(23845)
}


