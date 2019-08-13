# OAO EAC API Documentation

Official version of the EAC API (version 1.0). For questions or comments reach out via github, or leif.kramer@adops.com

# Request Format


Method: GET
Base URL: https://static.adsugar.ch/api
Parameters:


param|description|required|example 
 ---------- | ---------- | ---------- | ----------
 ord| randomly generated value, this should be unique for every request made to the API | no (if no value is being used, the ord parameter should be inlcuded in request, but given an empty string value.  Accepted values are alphanumeric| 998493ji9w9234
 c| randomly generated value, this should be unique for every request made to the API, if using the ord= parameter, this value should differ from the ord. Accepted values are alphanumeric | yes| 8765483jjc9
 t| A URI encoded set of custom targeting values (querystring params) associated with the targeting of line items in Ad Manager | yes (if no custom targeting, give a value of an empty string| t=pos%3Dtop%26foo%3Dbar 
 iu| the full ad unit path corresponding to the targeted ad unit | yes| /25434/news/sports
 sz| targeted ad size  | yes| sz=728x90 
 npa| non personalized targeting for GDPR compliance. If no included the value is defaulted to 1 (for no personalized targeting) | no| npa=1 or npa=0  
 ppid| unique publisher identifier for the user, to be used for custom ad targeting (should be a 32char hash) | no| ppid=0a196649d3aca92d3e06a90a1f28f60e 

# Response Format
Responses will always return  JSON-like object in the response, provided a valid HTTPS GET request has been made.
The  object will contain all relevant information for serving the creative and its click information.

Description of response object keys and expected usage:

Key Name | Description 
---------- | ----------
constructed_image_path | This is the URL to be loaded in the email (or other platform) that points to the creative. Deconstructed you will notice it has the downloaded impression URL, followed by the destination URL. The downloaded impression URL will redirect to the destination URL and ensure the impression is counted in Ad Manager and EAC
ad_click_url | The URL users should be sent to when clicking on the image creative. 
ad_image_url | The location of the destination creative served by the ad server. If using this instead of the constructed_image_path, the ad_impression_url MUST be executed as well, at (about) the same time as the ad_image_url. See ad_impression_url for more details
ad_impression_url | The impression tracking URL to ensure a downloaded impression signal is sent to the adserver. If using this separated out from the ad_image_url, this should be sent as a GET request, either server-side or embedded as a 1x1 in an email. This is required to ensure accurate tracking.
google_lineitem_id | The ID of the line item that was returned from Ad Manger 
google_creative_id | The id of the creative returned from Ad Manager 
eac_adcall_id | the unique id for the ad call, for internal EAC reference and troubleshooting |
status | A successful response will always be the value of "success", any error response will include some details about the error. 

## Example Request/Response ( with curl)

     $ curl -v https://static.adsugar.ch/api?iu=/50536250/oao_test/btstest/adsugar&t=test%3Dadsugar&sz=300x250&c=872349237&ord=z1b2c3v5y67e
     ---
    > GET /api?iu=/50536250/oao_test/btstest/adsugar&t=test%3Dadsugar&sz=300x250&c=872349237&ord=z1b2c3v5y67e HTTP/2
    > Host: static.adsugar.ch
    > User-Agent: curl/7.54.0
    > Accept: */*
    >
    * Connection state changed (MAX_CONCURRENT_STREAMS updated)!
    < HTTP/2 200
    < content-type: application/json
    < content-length: 1944
    < date: Tue, 13 Aug 2019 12:41:27 GMT
    < x-cache: Miss from cloudfront
    < via: 1.1 88b63cb2f8aab28c7291262ffc15282f.cloudfront.net (CloudFront)
    < x-amz-cf-pop: IAD89-C1
    < x-amz-cf-id: uc9LtIGGIUTozy1C3-JBsvRohyUYrPM6ajqGDMq8hIdvU5-pEWkvbQ==
    <
    {
    "constructed_ad_image_path": "https://securepubads.g.doubleclick.net/pcs/view?xai=AKAOjsvBAmo7a-PbBGjvFPbuaARINPwKyfpHzK2wCUGmGpWjkUt7SwR5wP7VpF7cjmPU6KdKn69UNUCfeljkaB2k79niwXe5c6rtp9h65Dt109NUGa_D5QzjH2WhDPCJMDIVNXdAtllt1jkBS2I9nisloaQAnyX-6mwkp_6QlUnjFGdDfqkWLeY22xFXPzDiHXOpnioCi-SZ-0Qzh_m-FoKpHnDqpav54VvpEDmNx4el-OyYz0MMmhTRtxUlUER1Rd69aS0TGHqqbg721ju6DF3zp8jaZtg&sig=Cg0ArKJSzKgH7_phZ5B_EAE&urlfix=1&adurl=https://tpc.googlesyndication.com/pagead/imgad?id=CICAgKCHj62QYxABGAEyCKMvnZw872s5", 
    "ad_click_url": "https://securepubads.g.doubleclick.net/pcs/view?xai=AKAOjsvBAmo7a-PbBGjvFPbuaARINPwKyfpHzK2wCUGmGpWjkUt7SwR5wP7VpF7cjmPU6KdKn69UNUCfeljkaB2k79niwXe5c6rtp9h65Dt109NUGa_D5QzjH2WhDPCJMDIVNXdAtllt1jkBS2I9nisloaQAnyX-6mwkp_6QlUnjFGdDfqkWLeY22xFXPzDiHXOpnioCi-SZ-0Qzh_m-FoKpHnDqpav54VvpEDmNx4el-OyYz0MMmhTRtxUlUER1Rd69aS0TGHqqbg721ju6DF3zp8jaZtg&sig=Cg0ArKJSzKgH7_phZ5B_EAE&urlfix=1&adurl=https://adclick.g.doubleclick.net/pcs/click?xai=AKAOjsvbal3Y1LMYCu2xNh4exT_Dd2O8hPFpj1dqOkMqMLmm55L7nNSIw6RnMJgOtnWGSUVtr8mBrq79gC4Y_xdzjeTvYv_L3X5gjyf0gQOlTLcKivtGSzWyeF6Vr7qkVJK93XI4QJl0ZFtgYSH5zP7v-kexjxZuk6S0BnuFZTAKMd1T5t_j1us-AhJr6OQDIDR_lz8OBjVdnMFt0wq3iw0QXUPPWcyDTKvKMSqRbpNStnjXxOreUe3fv8yoTxeCzYqiputUWQeuxhO5_nPfVnE&sig=Cg0ArKJSzDA3h3DNpIjmEAE&urlfix=1&adurl=http://nflc.om",
    "ad_image_url": "https://tpc.googlesyndication.com/pagead/imgad?id=CICAgKCHj62QYxABGAEyCKMvnZw872s5",
    "ad_impression_url": "https://securepubads.g.doubleclick.net/pcs/view?xai=AKAOjsvBAmo7a-PbBGjvFPbuaARINPwKyfpHzK2wCUGmGpWjkUt7SwR5wP7VpF7cjmPU6KdKn69UNUCfeljkaB2k79niwXe5c6rtp9h65Dt109NUGa_D5QzjH2WhDPCJMDIVNXdAtllt1jkBS2I9nisloaQAnyX-6mwkp_6QlUnjFGdDfqkWLeY22xFXPzDiHXOpnioCi-SZ-0Qzh_m-FoKpHnDqpav54VvpEDmNx4el-OyYz0MMmhTRtxUlUER1Rd69aS0TGHqqbg721ju6DF3zp8jaZtg&sig=Cg0ArKJSzKgH7_phZ5B_EAE&urlfix=1&adurl=", 
    "google_lineitem_id": "5131854464", 
    "google_creative_id": "138277497522", 
    "eac_adcall_id": "f4851b7448a2bc7723a9d78f0278ec16",
    "status": "success"
    }

   
