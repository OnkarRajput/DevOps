## response time vs page load time 

 #### page load time : 
 Load and display time is the time it takes for your website to be downloaded from the server(s) and displayed on the user's browser. 

Page load time is the time it takes to download and display the entire content of a web page in the browser window (measured in seconds).

Load Time is how long a specific page took to load in its entirety, this includes all images, scripts, CSS and third party resources. that might be found on a website. These reports will combine the load time of each element on the page to give you the total page load time, and this is why the load time of a website often is a lot higher than the response time.

The speed at which a page loads depends on the hosting server, amount of bandwidth in transit, and web page design – as well as the number, type, and weight of elements on the page. Other factors include user location, device, and browser type.

improve page load time: File Compression: Reduces the size of the CSS, HTML, JavaScript, images, and other web elements.
* Minification: Optimizes the code by removing unused code, white spaces, comments, etc.
*  Reducing redirects: Each time a web page redirects the browser to another server, the user faces an additional time for the request-response cycle to complete. Best to get rid of these if possible.
* CDN: A content delivery network (CDN) puts content geographically closer to your users so they can receive it quicker.

-------
### response time 

Response time is the length of time it takes for your website's functions to respond to the user's query.
Ex. the length of time for the modals to open, for your form to submit etc.

Server response time is the time that passes between a client requesting a page in a browser and a server responding to that request. It is measured by TTFB (Time to First Byte). TTFB is how many milliseconds it takes to receive the first byte of the page after sending an HTTP request.

Response time is the total amount of time it takes to respond to a request for service. That service can be anything from a memory fetch, to a disk IO, to a complex database query, or loading a full web page. Ignoring transmission time for a moment, the response time is the sum of the service time and wait time.

Response Time : 

For an uptime check (http check) the response time is calculated as the time it takes to perform a HTTP GET to the specified URL, so the response time is calculated in three parts:

Time to first byte
Time to receive headers
Time to load HTML of the site

---------

i. Latency : Your system stop reponding or your application very slow. everybody must have encounter

. So Latency means time take by client to server and again back from server to client. SO this delay or this communication which is happening over network. how much time is being spent over there.
The time taken for the request to reach the server and again back time taken by the responce to  reach the  browser. that time sepent over the network you can say  Latency.


------------------
ii. Response Time :  Supose that you are send request from  your web browser to the server and after how much time did you get the response. Means a request send from browser and browser recieved the response that the time spend that time gap occured that is the response time. 

Supose i am using google drive and and type somthing and all the changes take time to save, So how long does it take. That is the response time. So it's full you can say from request till we recieved  the response that is your response time. so its include the time taken by the request to reach the server. than time taken by the server to process the request and then time taken by the response from server to client.

This the combination of three things: the response time is compose of time taken by the request to go from client to server. e.g client can be a browser. So from client to server than time taken by server to proccess that request. and than time taken by the result of that request to reach back to the client.

It's combination of two perameter: 
Response Time = Processing Time + Latency

Latency : time spent over the communication chanel
Processing time : the time taken by server to produce the desire result 

---------------
iii. Processing Time : Processing time is the time taken by the server to process that request. Means it took the input, when it recieve the input and it produce the final response. 

So proccess time is the time taken by the server to proccess the request. Proccess means it will take the input, preform the computation what ever logic is being applyed. excute that logic and the produce the final responce. so this is all happening on server level

---------------
iv. Throughput : you have API, endpoint. how many request can be surved per unit of the time. how requests can be served in the sepcific time duration means per unit of time. that is what how you messured the Throughput. If you Latency is high than you Throughput will be less, if your Response time is higher Throughput will discrease. if Processing time is higher than Throughput will increase.

 
