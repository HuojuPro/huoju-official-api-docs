## Order 

Trading and Order related APIs. API path usually depend on account-group and account-category: 

  * account-group   : get your account-group from 'Account Info' API

  * account-category: always `cash`

For all order related ack or data, there is *orderId* field to identify the order. 

###
### Generate Order Id

We use the following method to generate an unique id for each order place/cancel request. (You could get `userUID` from `Account Info` API.)


#### Method
  
  * A = 'a' for order via rest api, or 's' for order via websocket;

  * B = Convert timestamp (in miliseconds) to hex string;
  
  * C = User UID (11 chars, starting with 'U' followed by 10 digits);
  
  * D = If user provide client order Id (with length >= 9, letters and digits only), then take the right 9 chars; otherwise, we randomly generate 9 chars;
  
  * Final order Id is concatenation of strings A, B, C, D from above steps, i.e., orderId = A + B + C + D.


#### Extra info on `id`
 
 * `id` value must satisfy regrex pattern "^\w[\w\-]*\w$" (i.e. start and end with word character), and with length up to 32.  ("Invalid Client Order id" error for violation)

 * `id` value with length 9 is recommended, since we take the right most 9 chars for order Id generation.

 * `id` value with length < 9 will not be used in order Id generation, but we still echo it back in order ack message (empty string when no `id` value provided).

 * If a valid `id` value is provided when placing order, order Id from server side is pre-determined. This could be helpful for order status check in case of accidently internet connection issue.

#### Code Sample

Please refer to python code to [gen server order id](https://github.com/HuojuPro/huoju-api-demo/blob/master/python/util.py)

