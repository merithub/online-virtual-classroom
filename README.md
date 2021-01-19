# online-virtual-classroom
Steps to integrate API:
Create JWT token from the {CLIENT_ID}, {CLIENT_SECRET}
Get authToken from MeritHub API
Add users in your account and get their userId
Schedule a class with authToken and userId
Add/Remove users in class with classId, authToken & userId
CLIENT_ID and networkId are the same.

You can generate JWT in any language and integrate our API in any server side language. We are providing a PHP sample here. If you need the same in any other language, please contact us. 

Create a jwt at your end to request a token (PHP).
Replace {CLIENT_ID}, {CLIENT_SECRET}  in php code.

function base64url_encode($str) {
    return rtrim(strtr(base64_encode($str), '+/', '-_'), '=');
}
 
function generate_jwt($headers, $payload, $secret = 'CLIENT_SECRET') {
	$headers_encoded = base64url_encode(json_encode($headers));
	
	$payload_encoded = base64url_encode(json_encode($payload));
	
	$signature = hash_hmac('SHA256', "$headers_encoded.$payload_encoded", $secret, true);
	$signature_encoded = base64url_encode($signature);
 
	$jwt = "$headers_encoded.$payload_encoded.$signature_encoded";
 
	return $jwt;
}
 
$headers = array('alg'=>'HS256','typ'=>'JWT');
$payload = array('aud'=>'https://s1.serviceaccountsapi.merithub.net/v1/{client_id}/api/token', 'iss'=>'CLIENT_ID', 'expiry'=> 3600);
 
$jwt = generate_jwt($headers, $payload);
 
echo $jwt;

Get access_token from api

Note: You can explore more about this standard at:
https://developers.google.com/identity/protocols/oauth2/service-account




Endpoint : https://s1.serviceaccounts.merithub.net/v1/{networkId}/api/token

POST    /v1/{networkId}/api/token  
HOST :   s1.serviceaccounts.merithub.net

Content-Type: application/x-www-form-urlencoded

Request body:
curl --location --request POST 'https://s1.serviceaccounts.merithub.net/v1/{networkId}/api/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'assertion=Bearer {GENERATED_JWT}' \
--data-urlencode 'grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer'




Response body:
{
"access_token": "ACCESS_TOKEN",
           "expires_in": “EXPIRY”,
"api_domain": "https://s1.serviceaccounts.merithub.net/{networkId}/api/token",
           "token_type": “Bearer”

}



To request in php:


 
$request = new HttpRequest();
$request->setUrl('https://s2.serviceaccounts.merithub.net/v1/{CLIENT_ID}/api/token');
$request->setMethod(HTTP_METH_POST);
 
$request->setHeaders(array(
 'content-type' => 'application/x-www-form-urlencoded'
));
 
$request->setContentType('application/x-www-form-urlencoded');
$request->setPostFields(array(
 'assertion' => '{GENERATED_JWT}',
 'grant_type' => 'urn:ietf:params:oauth:grant-type:jwt-bearer'
));
 
try {
 $response = $request->send();
 
 echo $response->getBody();
} catch (HttpException $ex) {
 echo $ex;
}


Add users in account:

Endpoint : https://s1.serviceaccounts.merithub.net/v1/{networkId}/users

POST    /v1/{networkId}/users   
HOST :   s1.serviceaccounts.merithub.net
HEADER : "Authorization": Bearer {AccessToken}

Content-Type: application/json

Note: 
Roles:
(Creator): C
(Member): M
Permissions:
(Course and Class creation): CC
(Course and Class Joining): CJ
(Public Library Management): PL


Request body :

{
	"name" : "name 1",
	"title" : "software engineer",
	"img" : "image_url",
	"desc" : "this id demo user",
	"lang" : "en",
     	  "clientUserId" : "1234xyz", // userId of your database
	"email" : "email@xyz.abc",
	"role" : "M",
	"timeZone" : "Asia/Kolkata",
	"permission" : "CJ"

}


Response body:
{
   "userId": "userId_of_the_added_user",
    "userToken": “Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx”
}
You may ignore this user specific user token.


To search users:

Endpoint : https://s1.serviceaccounts.merithub.net/v1/{networkId}/users?query={email or clientUserId or Name}

GET    /v1/{networkId}/users   
HOST :   s1.serviceaccounts.merithub.net
HEADER : "Authorization": Bearer {AccessToken}

Response body :

{
	“users”: [
		{
			 "userId" : "userId",
      			"networkId" : "networkId",    
      			"name":”name”,
     	 "title" :”title”,
     	 "img" : "img url",
      	"description" : "description",
      	"lang" : "en",
      	"email" : "email1@xyz.com",
      	"role" : “M”,
      	“timeZone" :”Asia/Kolkata”,
"permission" : “CJ”,
"clientUserId" :”123123”,

},
{
			 "userId" : "userId",
      			"networkId" : "networkId",    
      			"name":”name”,
     	 "title" :”title”,
     	 "img" : "img url",
      	"description" : "description",
      	"lang" : "en",
      	"email" : "email2@xyz.com",
      	"role" : “M”,
      	“timeZone" :”Asia/Kolkata”,
"permission" : “CJ”,
"clientUserId" :”123234”

}

]

}



Schedule a Class:

You can  schedule two types of classes. classType can be either oneTime or perma. In perma class, you can schedule the days for perma recurring classes. Perma class is scheduled only once and can be used as many as times you want to use. But one time class requires new class links to start every new session of online live class.

You can schedule days for perma recurring classes. For this you have to provide the sequence of numbers. Like 0 for sunday , 1 for monday , 2 for tuesday and so on.

Start time  of the class must be in rfc3339  format. the duration of the class is in minutes. login  parameter describes whether the scheduled class is signup  or  no-signup.

Valid  status  are :
up     for upcoming class 
lv      for live class
cp     for completed class
ex     for expired class
dl      for deleted class



Request:

Endpoint : https://s1.classes.merithub.net/v1/{networkId}/{userId}

POST    /v1/{networkId}/{userId}   
HOST :    s1.classes.merithub.net
HEADER : “Authorization”: Bearer {AccessToken}

Content-Type: application/json


Request body to schedule a Class :


{
      "title" : "Testing This Class",
      "startTime" : "2020-10-05T15:29:00+05:30",
      "recordingDownload": false,
      "duration" : 30,
      "lang" : "en",
      "tags" : "math",
      "timeZoneId" : "Asia/Kolkata",
      "description" : "This is  a Test Class",
      "type" : "perma",               // for one time classes use "type" : "oneTime"
      "access" : "private",
      "autoRecord" : false,
      "login" : false,
      "layout" : "CR",
      "status" : "up",
      "recording" : {
          "record" : true,
          "autoRecord": false, 
          "recordingControl": true 
      },
      "participantControl" : {
          "write" : false,
          "audio" : false,
          "video" : false
      },
      "schedule"  :  [2, 4, 5]
}
Response:

{
   "classId" :  "Scheduled_Class_ID",
   "commonLinks" : {
             "commonHostLink" : "Common__Host__Link__To__Join"
             "commonModeratorLink": "Common__Moderator__Link__To__Join"
              "commonParticipantLink": "Common__Participant__Link__To__Join"
  },
"hostLink": "Unique__User__Link__For ___User_"
}



You would want to  use hostLink to open the class:
https://live.merithub.com/info/room/{networkId}/{hostLink}
If you want to show device test page, You may put additional query string like:
https://live.merithub.com/info/room/{networkId}/{hostLink}?devicetest=true
 
Edit Class
You cannot change the type of class. You can edit the schedule only in case of perma class. On edit “type” parameter will be ignored.

Endpoint : https://s1.classes.merithub.net/v1/{networkId}/{classId}
PUT    /v1/{networkId}/{classId} 
HOST :    s1.classes.merithub.net
HEADER : “Authorization”: Bearer {AccessToken}
Content-Type: application/json

Request body :
{
      "title" : "Testing This Class",
      "startTime" : "2020-10-05T15:29:00+05:30",    /// RFC  3339
      "recordingDownload": false,
      "duration" : 30,
      "lang" : "en",
      "tags" : "tt",
      "timeZoneId" : "Asia/Kolkata",
      "description" : "This is Test Class",
      "autoRecord" : false,
      "login" : false,
      "layout" : "CR",
      "recording" : {
          "record" : true,
          "autoRecord": false, 
          "recordingControl": true 
      },
      "participantControl" : {
          "write" : false,
          "audio" : false,
          "video" : false
      },
      "schedule"  :  [2, 4, 5]
}
Response body :

{
   "message": "success"
}
 
Add users in class:
 
Endpoint :  https://s1.classes.merithub.net/v1/{networkId}/{classId}/users

POST    /v1/{networkId}/{classId}/users
HOST :    s1.classes.merithub.net
HEADER : “Authorization”: Bearer {AccessToken}

Content-Type: application/json

userLink -> It can be one of the links from the commonLinks returned by the create class.

Request body:

{
    "users": [
        {
            "userId": "bvr01nd5evglpdpmgcb0",
           "userLink" : "bb.NpgS0KgwUw8hBb0sP3ZyCPAKKhcQz_H3V197J0IGXZAQ6Wm
                        qNxUdi5a407Ng",    
            "userType": "su"
        }
    ]
}


Response body:
 
       {
           "userId": "bvgvbh86bjj8io7u80",
           "userLink":   "CPAKKhcQz_Hbb.NpgS0KgwUw8hBb0sP3Zy
3V197J0IGXZAQ6WmqNxUdi5a407Ng"
       }
 
 
 
Remove user from class

It removes the user from class. You have to send an array of userIds of those users who you want to remove from the class.

Endpoint : https://s1.classes.merithub.net/v1/{networkId}/{classId}/removeusers

POST    /v1/{networkId}/{classId}/removeusers
HOST :    s1.classes.merithub.net
HEADER : “Authorization”: Bearer {AccessToken}

Content-Type: application/json


If your class has only one host , then you cannot remove that one host from the class as class requires at least one host.

Request body :
{
   "users": [
       "User_ID_1",
       "User_ID_2"
   ]
}
 
  
 
 
Web hook to ping status, of classes, recording, attendance and content:
 
Sample Webhook Url:  https://yoururl.com/merithubstatuspings
Method: POST

 
You will receive the following data at status ping url for different objects:
 
Class is live
Body:
{
   "networkId": "network_Id",
   "classId": "class_Id",
   "status": "lv",
   "subClassId":"subClassId"
}
 
Class has ended
{
   "networkId": "network_Id",
   "classId": "class_Id",
   "status": "cp",
   "subClassId":"subClassId"
}
 
Class is cancelled
{
   "networkId": "network_Id",
   "classId": "class_Id",
   "status": "cl"
}
 
Class is expired
{
   "networkId": "network_Id",
   "classId": "class_Id",
   "status": "ex"
}
 
When class has been edited
{
 "networkId": "network_Id",
   "classId": "class_Id",
   "status": "up"
}
 
       6)   on uploading any file in library
{
   "networkId": "Network___ID",
   "userId": "Class____ID",
   "fileId": "File___ID",
   "status": "completed",
   "convertedUrl": "Converted_File_Link"
}
 
7)When class has ended and attendance has been generated:
{
   "networkId": "Network___ID",
   "classId": "Class____ID",
   "subClassId": "Subclass___ID",
   "attendance": ["array of attendance"]
}
 
8) When recording is available
{
   "networkId": "Network___ID",
   "classId": "Class____ID",
   "subClassId": "Subclass___ID",
   "recording": "Recording__Link"
}
 
 

