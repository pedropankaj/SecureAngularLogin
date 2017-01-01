## Simple Angular2 Login with CryptoJS
#### send message "encrypted"


 After long serach I didn't find any good and working Tutorials how to integrate crypto in Angular2 
 so i will give a little input how to do it - Maybe its not the best way but it works
 
 ***
 Remember AngularJS is on the client site.. your key will be visible for developers!!
 I use this to get a bit secure for a little man in the middle attack!! 
 Output:
 
 
 ![alt tag](http://desouza.ch/projekte/angular/login/img/login.png)
 
###### Requiremend
```npm
$ npm install crypto-js
```
More: https://www.npmjs.com/package/crypto-js

include the node_modul into your service or to your .ts file.
You can do this by adding this path:
###### Adding requiremend
```angular
import * as CryptoJS from '../../../node_modules/crypto-js';
or
// import CryptoJS = require('crypto-js');
```

## Usage

```Angular2
  //Get Login Infos
  loginfn(usercreds) {

    //Test Output 
    console.log(usercreds.name);
    console.log(usercreds.password);
    
    //Encrypt the Passwort with Base64
    var key = CryptoJS.enc.Base64.parse("#base64Key#");
    var iv  = CryptoJS.enc.Base64.parse("#base64IV#");
    //Impementing the Key and IV
    var encrypted = CryptoJS.AES.encrypt(usercreds.password, key, {iv: iv});

    //Save the new Login Data
    var secureUsercreds =
    {
      username: usercreds.username,
      password: encrypted.toString()
    };
    // Do some Stuff with the scure login
    }


```


## Server site

```NodeJS
 
//Handle the request
app.post("/auth", function(req, res) {
  
  Output start
 // console.log("recived request");
 
 //get BodyInformation
  var user    = req.body.name;
  var psw     = req.body.password;

 
  //SQL INJ. RegEX
  user = user.replace(/["']/g, "");
  user = user.replace(/["']/g, ""); 

  //Database Query
  connection.query('SELECT user, psw FROM users WHERE user = "'+user+'" AND psw = "'+psw+'" LIMIT 1' , ['Page', 1],

    // Authentication
      function (err, results) {
        //On Success
        try{                                        //on result
          console.log(results)
          if(results[0].user ){                       //if user exits
                                                       //send 200 + Query
            res.send({
              success: "true",
              name: results[0].user,
              session: req.sessionID
            });
            //close the connection
            res.end();
          }
          
          else {
            //send error
            res.send({
              err: err,
            });
            res.end()
         
          }
        }
        catch (err){console.log("sql statement incorrect")}})
    });



```

...for more info contact me
