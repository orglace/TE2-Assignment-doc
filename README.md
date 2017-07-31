# TE2-Assignment

## Assignment:
 
Stand-up a small microservice for us using the following spec:
 
Microservice spec: 
 
 A simple message subscription service that exposes the following functionality: 
* Create a subscription (Would have at least one parameter, which would be a list of message Types the subscription wants to keep track of) 
* Read the subscription 
* Update the subscription 
* Post a message (The message would have at least a ‘messageType’ property.) 
 
In the response to the 'read subscription’ we would like also see how many times a particular event type has been received by a subscription. There may be more than one subscription listening for the same event type(s). 
 
Please provide a runnable service written in a Java 8 technology (can be based on spring-boot or any other of your preference). 

Please implement this as a runnable service written with a Java 8 technology (can be based on the Spring Framework or any other of your preference).
Also provide an explanation as to instruct how a client would begin using your service, such as whether or not this would be via REST API, JMX, etc. 

## Solution
This is a Spring Boot Microservice solution. In the next secctions I'll gonna explain how to test it in an easy way.

Requirement to run the microservice:
* Java 8
* Maven

To run and test this solution please make the following steps.

1. First thing to do is clone the github repository to the local machine. So open a terminal en your computer and type:
```
$ git clone git@github.com:orglace/TE2-Assignment.git
$ cd TE2-Assignment
```
2. Once inside the project folder you can run the project using 'Maven' or 'Java'.
* Using maven. Just run the following command into terminal:
```
$ mvn spring-boot:run
```
* Using java. You need to package a java jar using maven, then you can execute the microservice jar using java virtual machine. Type the following in your terminal.
```
$ mvn package
$ java -jar target/subscription-service-0.0.1-SNAPSHOT.jar
```
No matters what option you choose, at the end you gonna have a microservice running at http://localhost:8080. To consume the microservice's RESTful API you can use a traditional browser for GET requests, or Postman or even CURL.

## Microservice RESTful API doc.

* To get the list of subscriptions this is the URL [http://localhost:8080/subscription/list]. So once you get your microservice up, try this URL using browser or CURL.
```
$ curl http://localhost:8080/subscription/list
```
Then you gonna get a json response of the subscriptions list.
```json
[
    {
        "id": 1,
        "email": "email_address1@example.com",
        "created": 1501525658300,
        "subscriptionTypes": [
            {
                "id": 1,
                "type": "Science"
            },
            {
                "id": 2,
                "type": "History"
            }
        ]
    },
    {
        "id": 2,
        "email": "email_address2@example.com",
        "created": 1501525658415,
        "subscriptionTypes": [
            {
                "id": 1,
                "type": "Science"
            },
            {
                "id": 3,
                "type": "Tech"
            },
            {
                "id": 4,
                "type": "Entertainment"
            }
        ]
    }
]
```
* To get an specific subscription details just use this URL [http://localhost:8080/subscription/1].
```
$ curl http://localhost:8080/subscription/1
```
Response
```json
 {
	"id": 1,
	"email": "email_address1@example.com",
	"created": 1501525658300,
	"subscriptionTypes": [
		{
			"id": 1,
			"type": "Science"
		},
		{
			"id": 2,
			"type": "History"
		}
	]
}
```
* To create a new subscription you need to make a POST request to this URL [http://localhost:8080/subscription/]. So let's try this.
```
$ curl -d '{"email":"email_address3@example.com","subscriptionTypes":[{"type":"Sport"},{"type":"History"}]}' -H "Content-Type: application/json" -X POST http://localhost:8080/subscription/
```
The response gonna be the new Subscription alreaded created.
```json
{
	"id": 3,
	"email": "email_address3@example.com",
	"created": 1501527950530,
	"subscriptionTypes": [
		{
			"id": 5,
			"type": "Sport"
		},
		{
			"id": 2,
			"type": "History"
		}
	]
}
```
So if we check the subscriptions list, we gonna see the new one in the list.
* Let say we want to update the subscription that we just create now. For this we are going to make a PUT request in this URL [http://localhost:8080/subscription/3] and pass news message type subscriptions in the JSON body request. You can even update the email address. If you take a look, the id (3) in the URL is the same that we get in our last submited subscription.
```
curl -d '{"email":"email_address3_updated@example.com","subscriptionTypes":[{"type":"Tech"},{"type":"Science"}]}' -H "Content-Type: application/json" -X PUT http://localhost:8080/subscription/3
```
Then we gonna get the same subscription id (3), but this time with a list of subscription's messages types diferents.
```json
{
    "id": 3,
    "email": "email_address3_updated@example.com",
    "created": 1501527950530,
    "subscriptionTypes": [
        {
            "id": 3,
            "type": "Tech"
        },
        {
            "id": 1,
            "type": "Science"
        }
    ]
}
```
* What about to get a report about how many messages a subscriber had received by messages types? You can try this URL [http://localhost:8080/subscription/1/report] to get a report by message classification for the subscription number (1).
```
curl http://localhost:8080/subscription/1/report
```
The response
```json
{
    "email": "email_address1_updated@example.com",
    "statistics": [
        {
            "type": "Politic",
            "received": 0
        },
        {
            "type": "Health",
            "received": 0
        }
    ]
}
```
* There is not messages reveived of types 'Politic' or 'Health'. Let's check the list of posted messages. You can get this hitting the following URL [http://localhost:8080/post/list]
```
curl http://localhost:8080/post/list
```
Response.
```json
[
    {
        "id": 1,
        "title": "Post Title 1",
        "body": "Post Body 1",
        "created": 1501524279883,
        "postClassification": {
            "id": 1,
            "type": "Entertainment"
        }
    },
    {
        "id": 2,
        "title": "Post Title 2",
        "body": "Post Body 2",
        "created": 1501524279885,
        "postClassification": {
            "id": 1,
            "type": "Entertainment"
        }
    }
]
```
* There are two 'Entertainment' messages posted, so, because neither of them is about 'Politic' or 'Health' is the reason why the report give us zero messages received by the subscription number (1). So let's create a Post about 'Health' and then check it out the report again to see what happen. To make this, do a POST resquest to this URL [http://localhost:8080/post/].
```
curl -d '{"title":"Post of health","body":"Health Post Body","postClassification":{"type":"Health"}}' -H "Content-Type: application/json" -X POST http://localhost:8080/post/
```
You gonna have as response the new Post message already created.
```json
{
	"id": 3,
	"title": "Post of health",
	"body": "Health Post Body",
	"created": 1501530806080,
	"postClassification": {
		"id": 7,
		"type": "Health"
	}
}
```
So if you check the report for the first subscription again, you gonna see that the subscriber already received a message of type 'Health'.
```json
{
    "email": "email_address1@example.com",
    "statistics": [
        {
            "type": "Politic",
            "received": 0
        },
        {
            "type": "Health",
            "received": 1
        }
    ]
}
```



