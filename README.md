# OAO EAC API Documentation

Official version of the EAC API (version 1.0). For questions or comments reach out via github, or leif.kramer@adops.com

# Request Format

Method: GET
Base URL: https://static.adsugar.ch/api

Headers:

name|description
 ---------- | ----------
 User-Agent | User Agent of the device the ad will show on. If Serverisde an arbitrary  unique identifier of your System as User Agent is fine.
 X-Forwarded-For | String of IP addresses 
 
 
Parameters:
*Note request parameters for native and display are identical, native format, however, will return a different JSON format, further examples are below*


param|description|required|example 
 ---------- | ---------- | ---------- | ----------
 ord| randomly generated value, this should be unique for every request made to the API | no (if no value is being used, the ord parameter should be inlcuded in request, but given an empty string value.  Accepted values are alphanumeric| 998493ji9w9234
 c| randomly generated value, this should be unique for every request made to the API, if using the ord= parameter, this value should differ from the ord. Accepted values are alphanumeric | yes| 8765483jjc9
 t| A URI encoded set of custom targeting values (querystring params) associated with the targeting of line items in Ad Manager | yes (if no custom targeting, give a value of an empty string| t=pos%3Dtop%26foo%3Dbar 
 iu| the full ad unit path corresponding to the targeted ad unit | yes| /25434/news/sports
 sz| targeted ad size  | yes| sz=728x90 
 npa| non personalized targeting for GDPR compliance. If no included the value is defaulted to 1 (for no personalized targeting) | no| npa=1 or npa=0  
 ppid| unique publisher identifier for the user, to be used for custom ad targeting (should be a 32char hash) | no| ppid=0a196649d3aca92d3e06a90a1f28f60e 

# Response Format DISPLAY ONLY
Responses will always return  JSON-like object in the response, provided a valid HTTPS GET request has been made.
The  object will contain all relevant information for serving the creative and its click information.

Description of response object keys and expected usage:

Key Name | Description 
---------- | ----------
constructed_image_path | This is the URL to be loaded in the email (or other platform) that points to the creative.
ad_click_url | The URL users should be sent to when clicking on the image creative. 
ad_image_url | The location of the destination creative served by the ad server. If using this instead of the constructed_image_path, the ad_impression_url MUST be executed as well, at (about) the same time as the ad_image_url. See ad_impression_url for more details
ad_impression_url | The impression tracking URL to ensure a downloaded impression signal is sent to the adserver. If using this separated out from the ad_image_url, this should be sent as a GET request, either server-side or embedded as a 1x1 in an email. This is required to ensure accurate tracking.
google_lineitem_id | The ID of the line item that was returned from Ad Manger 
google_creative_id | The id of the creative returned from Ad Manager 
eac_adcall_id | the unique id for the ad call, for internal EAC reference and troubleshooting |
status | A successful response will always be the value of "success", any error response will include some details about the error. 
html_format | The code rendered for HTML delivery.

## Example Request/Response (with curl) DISPLAY ONLY

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
    "html_format": "<a href='https://securepubads.g.doubleclick.net/pcs/view?xai=AKAOjst-Xsgh6WhWLnwn5VIzta1fSezS14c3gDyT8dLcr1FF0dpFjyzFclC6GKU0j2-1NnuJNCWwUA9oBOTGZvDPU9BJpQSS79SqJbzGIT8FcoTppRe5-vwwF8fS4_eMwFanyOeB-Zd5SCk4fVCk-G-PKeTo5Mard90BcS6KsG6P6qN-24L_C2iPqYLiZSSkPEX62zSUcy4oRyK3idr-Etpo0z_VvvBVv-VBD47Nt52XKKafLstLUDEGx1IS2-q40K9IDxUG6UJrXYgyshCc9JoDaf8zDDMFvfaN&sig=Cg0ArKJSzMKf4Yaw28ZHEAE&urlfix=1&adurl=https://adclick.g.doubleclick.net/pcs/click?xai=AKAOjsvGWw97nxpWgcMn0qxZ9PrGG0LobajRB7O6Dqzz1GzHqdMONCp5lqTbiK9VEynunw08QjVs0BNcV_nqu704iQDID49zosBPVOtLSY-xH8DZbLzumuJWVZZSuf5JyAo_Ljcdm9GkTSvaitbzNPtJ3WnGW9PCvWYCdsBMLzCubKD9GW2QWQlB12-0jzmvajaaG62qBc8kQyb1ctiU-t0WH98JSObOayMX_1QweCIdMhZRLQdw9284oXKiyymJszVfjS7p1tAnfjD3Evv92Bauh6jr&sig=Cg0ArKJSzCosWtZY63YAEAE&urlfix=1&adurl=http://bit.ly/HUappstore' target='_blank'><img src='https://securepubads.g.doubleclick.net/pcs/view?xai=AKAOjst-Xsgh6WhWLnwn5VIzta1fSezS14c3gDyT8dLcr1FF0dpFjyzFclC6GKU0j2-1NnuJNCWwUA9oBOTGZvDPU9BJpQSS79SqJbzGIT8FcoTppRe5-vwwF8fS4_eMwFanyOeB-Zd5SCk4fVCk-G-PKeTo5Mard90BcS6KsG6P6qN-24L_C2iPqYLiZSSkPEX62zSUcy4oRyK3idr-Etpo0z_VvvBVv-VBD47Nt52XKKafLstLUDEGx1IS2-q40K9IDxUG6UJrXYgyshCc9JoDaf8zDDMFvfaN&sig=Cg0ArKJSzMKf4Yaw28ZHEAE&urlfix=1&adurl=https://tpc.googlesyndication.com/pagead/imgad?id=CICAgKCHv4rk7QEQARgBMggO4XVnebgvGg'></a>",
    "status": "success"
    }

# Response Format NATIVE ONLY
Responses will always return  JSON-like object in the response, provided a valid HTTPS GET request has been made.
The  object will contain all relevant information for serving the creative and its click information.

Description of response object keys and expected usage:

Key Name | Description 
---------- | ----------
image | This is the URL to be loaded in the email (or other platform) that points to the image path to be used in the native unit.
ad_click_url | The URL users should be sent to when clicking on the image or text in the native unitcreative. 
header | This is the text heading typically seen as a "title" in a native unit. I can contain text or HTML for additional formatting.
subheader | An optional subheading block of text, often used below a header or title in a native ad unit. This can contain plaintext or HTML.
text | The text content of the native unit- this can contain plaintext or additional text/HTML if needed.
native | hardcoded response value of "1" (as an integer, not string). When present, informs the API response listener that this is a native ad.

## Example Request/Response (with curl) NATIVE ONLY


     curl -v https://static.adsugar.ch/api?ord=7987&c=6546&iu=/50536250/oao_test/btstest/adsugar&t=test%3Dnativetest&sz=100x100
    
    > GET /api?ord=7987&c=6546&iu=/50536250/oao_test/btstest/adsugar&t=test%3Dnativetest&sz=100x100 HTTP/2
    > Host: static.adsugar.ch
    > User-Agent: curl/7.64.1
    > Accept: */*
    >
    * Connection state changed (MAX_CONCURRENT_STREAMS == 128)!
    < HTTP/2 200
    < content-type: application/json
    < content-length: 1620
    < date: Tue, 25 Feb 2020 13:50:58 GMT
    < x-amzn-requestid: 09c19d42-d3dc-4c27-bcc5-2fc99689d139
    < access-control-allow-origin: *
    < amp-access-control-allow-source-origin: amp@gmail.dev
    < access-control-allow-headers: amp4email-proxy-assertion
    < x-amz-apigw-id: IdLqYHsTIAMFeMA=
    < access-control-expose-headers: AMP-Access-Control-Allow-Source-Origin
    < x-amzn-trace-id: Root=1-5e552642-34db3e809fdb16e0cf709114;Sampled=0
    < x-cache: Miss from cloudfront
    < via: 1.1 6ed623541a1487ecd1bc71b49417e87c.cloudfront.net (CloudFront)
    < x-amz-cf-pop: LAX3-C1
    < x-amz-cf-id: 8Ew4sp5eDqwHtGRe2iOD0G3GLa54pNFriunMtC3bAmmi_XHWKTjGWg==
    <
    * Connection #0 to host static.adsugar.ch left intact
    {"image": "https://securepubads.g.doubleclick.net/pcs/view?xai=AKAOjssabmEkNli8hxG8YaZV9fBySP62nX19nPBc6wJKojQvD2SdIUPSq_6vwtXEigUfkd1uwgR0IZZqMMzwMH_dbjs4Ik-Jt_wG65SdzVMATmbcgszfqXHqbWjXGEi9FhZKuGclKrJ35eCGGo8qK5SEGlLr_bvePK-4IB6Ap_3EXSn0OkOOUW7RmkNStaXl2Ymk7ec-jWJm-CpPjutjNal7QqZYFwYVVc2tdTixvI934VWVlFLcHVF4fbp2znl8AU5IFsImXt7QvoDaqTxMJ37Ub4BXqD8&sig=Cg0ArKJSzGQSvQdTOlQZEAE&urlfix=1&adurl=https://d2bmyofwy5n4pb.cloudfront.net/91820ABD-A860-2220-5960BE1F05A26323.png",
    "header": "This is an example header",
    "subheader": "Everything you ever wanted to know",
    "text": "Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. Ut dolor magna, interdum eu pulvinar eget, volutpat id quam. In fringilla mauris ac finibus vulputate. Quisque non auctor ante. Suspendisse potenti.",
    "click_url": "https://securepubads.g.doubleclick.net/pcs/view?xai=AKAOjssabmEkNli8hxG8YaZV9fBySP62nX19nPBc6wJKojQvD2SdIUPSq_6vwtXEigUfkd1uwgR0IZZqMMzwMH_dbjs4Ik-Jt_wG65SdzVMATmbcgszfqXHqbWjXGEi9FhZKuGclKrJ35eCGGo8qK5SEGlLr_bvePK-4IB6Ap_3EXSn0OkOOUW7RmkNStaXl2Ymk7ec-jWJm-CpPjutjNal7QqZYFwYVVc2tdTixvI934VWVlFLcHVF4fbp2znl8AU5IFsImXt7QvoDaqTxMJ37Ub4BXqD8&sig=Cg0ArKJSzGQSvQdTOlQZEAE&urlfix=1&adurl=https://adclick.g.doubleclick.net/pcs/click?xai=AKAOjsvqCGNO8g_pqcdiftqkf57I8f6RT_TCodnr2zYN0uTGnohUTcaWpN4EvuUXdL8z4pGj2a9D6Xlxis2To2qQuYeb6SANWWdxcHKVb0C0VxZRAKAi3zG1oEioAMTbyNqf5koxiWCwB8ONlse3ZJdJY5T7DC7LVXhKMrCmqy58shmzdoKbNu8zQ6G1h03RrBWSpD1ndvzZIdshrtWQhyhEyyO-3_F9Dc00phZl6mA_o7SNccnEdRulQZsWpp5gQaLn3U36i-vKeazeVFwdj9A&sig=Cg0ArKJSzOEKBXJzXRnKEAE&urlfix=1&adurl=https://www.adops.com",
    "native": 1}
