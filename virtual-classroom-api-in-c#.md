# Create a jwt at your end to request a token (C#).


using System;
using System.Text;
using Microsoft.IdentityModel.Tokens;
using Newtonsoft.Json;
using System.Security.Cryptography;

namespace ConsoleApp6
{
    class Program
    {
        static void Main(string[] args)
        {
            var header = new
            {
                alg = "HS256",
                typ = "JWT"
            };

            var headerPart = Base64UrlEncoder.Encode(JsonConvert.SerializeObject(header));

            var payload = new
            {
               // userId = "",
                aud = "https://s1.serviceaccountsapi.merithub.net/v1/bvgamh2ckrg7stsqi7hg/api/token",
                iss = "bvgamh2ckrg7stsqi7hg",
                expiry = 3600

            };

            var payloadPart = Base64UrlEncoder.Encode(JsonConvert.SerializeObject(payload));

            var secret = "$2a$04$CVV0zL0z/U9J8fzp9GbMaeI.wzQYGO8uEZwZk.cc3i5gWw.btc8Iy";
            var sha256 = new HMACSHA256(Encoding.UTF8.GetBytes(secret));
            var hashBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes($"{headerPart}.{payloadPart}"));
            var hash = Base64UrlEncoder.Encode(hashBytes);

            var jwt = $"{headerPart}.{payloadPart}.{hash}";

            Console.WriteLine(jwt);

            Console.ReadKey();

        }

        
    }
}

# 1. Get Session Token

var client = new RestClient("https://s1.serviceaccounts.merithub.net/v1/bvgamh2ckrg7stsqi7hg/api/token");
client.Timeout = -1;
var request = new RestRequest(Method.POST);
request.AddHeader("Content-Type", "application/x-www-form-urlencoded");
request.AddParameter("assertion", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3MxLnNlcnZpY2VhY2NvdW50c2FwaS5tZXJpdGh1Yi5uZXQvdjEvYnZnYW1oMmNrcmc3c3RzcWk3aGcvYXBpL3Rva2VuIiwiaXNzIjoiYnZnYW1oMmNrcmc3c3RzcWk3aGciLCJleHBpcnkiOjM2MDB9.Iax9NxIcuy9MHIzjxG52SYKneJfnaQ9fk6-_mdw8GNY");
request.AddParameter("grant_type", "urn:ietf:params:oauth:grant-type:jwt-bearer");
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);

# 2. Add user in network
var client = new RestClient("https://s1.serviceaccounts.merithub.net/v1/bvgamh2ckrg7stsqi7hg/users");
client.Timeout = -1;
var request = new RestRequest(Method.POST);
request.AddHeader("Authorization", "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJNSFMiOiJwUTNMQTJEIiwiZXhwIjoxNjEwOTEyMzU1LCJpZCI6ImJ2Z2FtaDJja3JnN3N0c3FpN2hnIiwibnQiOiJidmdhbWgyY2tyZzdzdHNxaTdoZyIsInBtIjoiVU0gUE0gQkwgQ0MgUEwiLCJybCI6IkEiLCJ0eiI6IkFzaWEvS29sa2F0YSIsInV0Ijoic3UifQ.w9c3Eo4tvhbpnc5QWvyw2McW-tFf8D2AFSdL7GW84S8 ");
request.AddHeader("Content-Type", "application/json");
request.AddParameter("application/json", "{\n\t\"name\" : \"name\",\n\t\"title\" : \"software engineer\",\n\t\"img\" : \"image_url\",\n\t\"desc\" : \"this id demo user\",\n\t\"lang\" : \"en\",\n \"clientUserId\" : \"1234xyz\", \n\t\"email\" : \"email@xyz.abc\",\n\t\"role\" : \"M\",\n\t\"timeZone\" : \"Asia/Kolkata\",\n\t\"permission\" : \"CJ\"\n}\n", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);
# 3. To search a user in network

var client = new RestClient("https://s1.serviceaccounts.merithub.net/v1/bvgamh2ckrg7stsqi7hg/users?query=email@xyz.abc");
client.Timeout = -1;
var request = new RestRequest(Method.GET);
request.AddHeader("Authorization", "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJNSFMiOiJwUTNMQTJEIiwiZXhwIjoxNjEwOTEyMzU1LCJpZCI6ImJ2Z2FtaDJja3JnN3N0c3FpN2hnIiwibnQiOiJidmdhbWgyY2tyZzdzdHNxaTdoZyIsInBtIjoiVU0gUE0gQkwgQ0MgUEwiLCJybCI6IkEiLCJ0eiI6IkFzaWEvS29sa2F0YSIsInV0Ijoic3UifQ.w9c3Eo4tvhbpnc5QWvyw2McW-tFf8D2AFSdL7GW84S8");
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);

# 4. Schedule a class
var client = new RestClient("https://s1.classes.merithub.net/v1/bvgamh2ckrg7stsqi7hg/bvgamh2ckrg7stsqi7hg");
client.Timeout = -1;
var request = new RestRequest(Method.POST);
request.AddHeader("Authorization", "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJNSFMiOiIwdVVxVk5yIiwiZXhwIjoxNjEwOTE3MzU5LCJpZCI6ImJ2Z2FtaDJja3JnN3N0c3FpN2hnIiwibnQiOiJidmdhbWgyY2tyZzdzdHNxaTdoZyIsInBtIjoiVU0gUE0gQkwgQ0MgUEwiLCJybCI6IkEiLCJ0eiI6IkFzaWEvS29sa2F0YSIsInV0Ijoic3UifQ.tMZgnL2qI-qnDtDCHlok_xT7O6u-Z8W41-v3pW8N_is");
request.AddHeader("Content-Type", "application/json");
request.AddParameter("application/json", "{\n \"title\" : \"Testing This Class\",\n \"startTime\" : \"2021-02-05T15:29:00+05:30\",\n \"recordingDownload\": false,\n \"duration\" : 30,\n \"lang\" : \"en\",\n \"tags\" : \"math\",\n \"timeZoneId\" : \"Asia/Kolkata\",\n \"description\" : \"This is a Test Class\",\n \"type\" : \"perma\", \n \"access\" : \"private\",\n \"autoRecord\" : false,\n \"login\" : false,\n \"layout\" : \"CR\",\n \"status\" : \"up\",\n \"recording\" : {\n \"record\" : true,\n \"autoRecord\": false, \n \"recordingControl\": true \n },\n \"participantControl\" : {\n \"write\" : false,\n \"audio\" : false,\n \"video\" : false\n },\n \"schedule\" : [2, 4, 5]\n}\n", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);
