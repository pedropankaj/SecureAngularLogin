 README.md

Simple Angular2 Login with CryptoJS



Example with Ionic 2 and Node JS



After long serach I didn't find any good and working Tutorials how to integrate crytojs in Angular2

so i will give a little input how to do it - Maybe its not the best way but it works and its secure!



Requiremend



$ npm install crypto-js

More: https://www.npmjs.com/package/crypto-js



Insert the Node_modul into your service or to your .ts

you can do this by adding this path:



adding requiremend



import * as CryptoJS from '../../../node_modules/crypto-js';

or

// or: import CryptoJS = require('crypto-js');

Usage







  //Get Login Infos

  loginfn(usercreds) {



    //Test Output 

    console.log(usercreds.password);

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



