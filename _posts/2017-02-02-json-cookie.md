---
layout: default
title:  "Parsing cookies stored in JSON"
date:   2017-02-02 20:22:00 +0800
---

Sometimes, you will have to store cookie data and you would store it in JSON with `JSON.stringify`. 

How would you parse out the cookie in JSON format back into a string separated by semicolons?

Here is how you would parse it back to a string (*requires Node.JS*):

Assuming your `cookie_file.txt` contains the follows:

```
[{"domain":".domain.com","httponly":false,"name":"presence","path":"/","secure":true,
  "value":"EDvF3EtimeF1486031553EuserFA2616400242A2EstateFDutF148asdasd1231235CEchFDp_5f616400242F3CC"},
  {"domain":".domain.com","httponly":false,"name":"p","path":"/","secure":false,"value":"-2"}]
```

The code to parse back the cookie that is stored in JSON:

```
var fs = require('fs');

fs.readFile('./cookie_file.txt', function read(err, data){
   var cookies = JSON.parse(data);
   baked_cookies = [];
   cookies.forEach(function(cookie){
     var add_ingredients = cookie.name + '=' + cookie.value + ';';
     baked_cookies.push(add_ingredients);
   });

   baked_cookies = baked_cookies.join(' ').slice(0, -1);
   console.log(baked_cookies);
});
```
Assuming the above code is saved as `cookie_parser.js`, you can run it like this:

`node cookie_parser.js`

output: `presence=EDvF3EtimeF1486031553EuserFA2616400242A2EstateFDutF148asdasd1231235CEchFDp_5f616400242F3CC; p=-2`
