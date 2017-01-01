## Simple Angular2 Login with CryptoJS
#### send message "encrypted"


 After long serach I didn't find any good and working Tutorials how to integrate crypto in Angular2 
 so i will give a little input how to do it - Maybe its not the best way but it works
 
 ***
 *Remember AngularJS is on the client site.. your key will be visible for developers!!
 I use this to get a bit secure against a little man in the middle attack!! <br> 
 <br>
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
    // Do some Stuff with the scure login
    }

```


## Example with NodeJS 


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

    //Encrypt the Passwort 
    //Man in the Middle Secure v1
    var key = CryptoJS.enc.Base64.parse("HackersSeeIT2");
    var iv  = CryptoJS.enc.Base64.parse("#base64IV#");
    var encrypted = CryptoJS.AES.encrypt(usercreds.password, key, {iv: iv});

    //Save the Login in to an Array
    var secureUsercreds =
    {
      username: usercreds.username,
      password: encrypted.toString()
    };


    //Set Infos Ready to Send
    var creds = 'name=' + secureUsercreds.username + '&password=' + secureUsercreds.password;

    //Encrypt Request Message
    var password = 'HackersSeeIT';
    var encrypted = CryptoJS.AES.encrypt(creds, password);
    var encryptedmessage = encrypted.toString();



    return new Promise((resolve) => {
      try {
        this._http.post('http://' + this.ipAdress + ':' + this.port + '/Auth', 'message=' + encryptedmessage, {headers: headers}).subscribe((data) => {
         // console.log(data.json()) output
         
         //IF Server sends 200
          if (data.json().success == true) {
    
           //do Stuff.. example:
           // this.isLoggedin = true;

          } else {
            console.log("Login error")
          }
          resolve(this.isLoggedin)
        })
      }catch (err){console.log("Login error")}
    })

  }
  
```

```
NodeJS

//SERVER SITE

//After it was passed to the next layer of middleware

    console.log("Request Recived")
  //Encrypt Req
   var obj = myParse(req);

  //Declare User info
  var user = obj.name;
  var temppsw =  obj.password;

  var key = CryptoJS.enc.Base64.parse("ParseTwice2");
  var iv  = CryptoJS.enc.Base64.parse("#base64IV#");
  var psw  = CryptoJS.AES.encrypt(temppsw , key, {iv: iv});
      psw = psw.toString();

  //prepara RegEX
  user = user.replace(/["']/g, "");
  user = user.replace(/["']/g, ""); //sql will replace  "'"  with \'


  //Database Query
  console.log(user + " Login attempt")
  connection.query('SELECT LoginID, Username, Passwort FROM Login WHERE Username = "'+user+'" AND Passwort = "'+psw+'" LIMIT 1' , ['Page', 1],
    function (err, results)  {
      if(results){
        //On Success
        try{
          console.log(results[0].Username)

          //Check if not an injection
          LoginID = results[0].LoginID;
          Username = results[0].Username;
          if(results[0].Username != 'sqjINJSave' ){

            //Get full User Information
            console.log(Username + " has Logged in. IDnr :"  + LoginID);
            req.session.user = Username;  //Save Session

            var info = getMitarbeiterInfo(LoginID);
            info.then(function (results) {

                if(results) {
                  //Tell Database Session ID
                  connection.query('UPDATE Login SET ' +
                    'SessionID = "'+ req.sessionID +'", ' +
                     'expires = "'+ now +'"' +
                      ', ipadress = "'+ req.connection.remoteAddress +'" where LoginID =' + LoginID , ['Page', 1]);

                    //200 Just for DEMO 
                   res.status(200).send({
                    success:true,
                    username: Username,
                    nachname: results[0].Nachname,
                    email: results[0].Email,
                    telefon: results[0].Telefon,
                    message: 1,
                    cockie: req.cookies,
                    session: req.sessionID,
                    ipadress: req.connection.remoteAddress,
                    Sessionipadress: req.session.ip,
                    SignedCookies: req.signedCookies,
                    expiried: ((req.session.cookie.maxAge / 36000) + 's')
                  });

                  return"";
                }});

          }else {


            res.end({
              success:false
            });
            return err
          }
        }
        catch (err)
        {console.log("login error")
          res.send({
            status:"wellcome" ,
            success: 'failed',
          });
          res.end();
          return;
        }
      }else {
        res.send({
          status:"wellcome" ,
          success: 'failed',
        });
        res.end();
        return err;
      }
    })


});


```
Decrypt message 
```
//Decrypt Request and parse to JSON
function myParse(req) {


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
