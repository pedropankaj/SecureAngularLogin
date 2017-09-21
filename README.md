## Simple Angular2 Login with CryptoJS
#### send message "encrypted"
 
 ***
 *Remember Angular and Javasript is a clientsite skript .. your key will be visible!!
 
 <br>
 Output:
 
 
 ![alt tag](http://desouza.ch/projekte/angular/login/img/login.png)
 
###### Requiremend
```npm
$ npm install crypto-js
$ npm install @types/crypto-js --save-dev
```
More: https://www.npmjs.com/package/crypto-js

include the node_module into your service or to your .ts file.

###### Adding requiremend
```angular
import * as CryptoJS from 'crypto-js';
//or
// import CryptoJS = require('crypto-js');
```

## Simple - Usage

```Angular2

  //encrypt Pass - Login 
  loginfn(usercreds) {

    //Test Output - just for the demo
    console.log(usercreds.name);
    console.log(usercreds.password);
    
    //Encrypt the Passwort with Base64
    var key = CryptoJS.enc.Base64.parse("#base64Key#");
    var iv  = CryptoJS.enc.Base64.parse("#base64IV#");
    
    //Impementing the Key and IV and encrypt the password
    var encrypted = CryptoJS.AES.encrypt(usercreds.password, key, {iv: iv});

    //Save the new Login Data
    var secureUsercreds =
    {
      username: usercreds.username,
      password: encrypted.toString()
    };
    // Do some Stuff with the secure login
    }

```


## Example with Angular & NodeJS 


```
Angular2

//CLIENT SITE

//Send Login to Server
  login(usercreds) {

    //set Standart Values
    this.isLoggedin = false;
    var headers = new Headers();
    
    //set Header 
    headers.append('Content-Type', 'application/X-www-form-urlencoded');

    //Encrypt the Password 
    //Man in the Middle Secure v1
    var key = CryptoJS.enc.Base64.parse("HackersSeeIT2");
    var iv  = CryptoJS.enc.Base64.parse("#base64IV#");
    var encrypted = CryptoJS.AES.encrypt(usercreds.password, key, {iv: iv});

    //Save the Login in to an array
    var secureUsercreds =
    {
      username: usercreds.username,
      password: encrypted.toString()
    };

    var creds = 'name=' + secureUsercreds.username + '&password=' + secureUsercreds.password;

    //Encrypt the whole body 
    var password = 'HackersSeeIT';
    var encrypted = CryptoJS.AES.encrypt(creds, password);
    var encryptedmessage = encrypted.toString();

    //POST ->
    return new Promise((resolve) => {
      try {
        this._http.post('http://' + this.ipAdress + ':' + this.port + '/Auth', 'message=' + encryptedmessage, {headers: headers}).subscribe((data) => {
              
           //do Stuff.. 
               
        })
      }catch (err){}
    })

  }
  
```

```
NodeJS

//SERVER SITE

//After it was passed to the next layer of middleware
app.post('/Auth', function(req, res) {

    console.log("Request Recived")
  //Encrypt Req
   var obj = parser(req);

  //Declare User info
  var user = obj.name;
  var temppsw =  obj.password;

  var key = CryptoJS.enc.Base64.parse("ParseTwice2");
  var iv  = CryptoJS.enc.Base64.parse("#base64IV#");
  var psw  = CryptoJS.AES.encrypt(temppsw , key, {iv: iv});
      psw = psw.toString();

  user = user.replace(/["']/g, "");
  user = user.replace(/["']/g, ""); 


  //Database Query
  console.log(user + " Login || Example")
  connection.execute('SELECT LoginID, Username, Passwort FROM Login WHERE Username = ? AND Passwort = ? LIMIT 1' ,[user,psw], 
    function (err, results)  {
         //DO STUFF
      })
})


```
Decrypt message 
```
//Decrypt Request
function parser(req) {


  //Client sliet Passwort
  var password = 'HackersSeeIT';
  //Convert to Base64


  const a = req.body.message.toString().replace(" ", "+");
  var b=a.replace(" ", "+");

  //Decrypt Message
  var decrypted = CryptoJS.AES.decrypt(b, password);
  var c = decrypted.toString(CryptoJS.enc.Utf8);


  //Convert to Object
  var d = '{"'+c.replace('="', ':')+'"}';
  var e = d.replace("&", '","');

  //Replace Prams to OBJ
  var f = e.replace("password=", 'password":"');
  var g = f.replace("name=", 'name":"');
  var h = g.toString();
  try {
    var OBJ = JSON.parse(h);
  }catch(err){myParse(req)}

  //Return
   return OBJ;
}


```
