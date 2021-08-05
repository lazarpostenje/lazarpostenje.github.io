---
author: "Lazar"
title: "Bypassing file upload restrictions"
date: "2021-08-04"
description: "Some of the shell upload tips and tricks I'd like to share"
tags: ["OSWE", "Web hacking", "Shell upload", "RCE"]
ShowToc: true
ShowBreadCrumbs: true
---

Never trust the user input!

# Introduction
Uploaded files can pose a significant risk to web applications which means the security side of file upload forms must be at the maximum level. During penetration testing engagements, you may have seen unrestricted file uploads which can quickly grant you **RCE**, but it's not always that easy. In some cases you have to bypass certain restrictions and trick the target application into uploading your malicious shell.

## The impact
The impact of this vulnerability is quite high, supposed code can be executed in the server context or on the client side. The likelihood of detection for the attacker is high. The prevalence is common. As a result the severity of this type of vulnerability is high.

# Client-side filters bypass
**What are client-side filters?** They are just another security measure designed to make user experience good, although not quite secure. It involves **JavaScript** checking the validity of uploaded file which is handy when normal web app users make an error and upload the wrong file. On the other hand, it heavily relies on user's browser
which can be easily bypassed by intercepting the traffic.

I won't be explaining how to intercept traffic using **Burp Suite** as there are like million articles on web explaining to do so. Just google it.

```javascript
<script type = "text/javascript" >
    var _validFileExtensions = [".jpg", ".jpeg", ".bmp", ".gif", ".png"];
function Validate(oForm) {
    var arrInputs = oForm.getElementsByTagName("input");
    for (var i = 0; i < arrInputs.length; i++) {
        var oInput = arrInputs[i];
        if (oInput.type == "file") {
            var sFileName = oInput.value;
            if (sFileName.length > 0) {
                var blnValid = false;
                for (var j = 0; j < _validFileExtensions.length; j++) {
                    var sCurExtension = _validFileExtensions[j];
                    if (sFileName.substr(sFileName.length - sCurExtension.length, sCurExtension.length).to LowerCase() == sCurExtension.toLowerCase()) {
                        blnValid = true;
                        break;
                    }
                }

                if (!blnValid) {
                    alert("Sorry, " + sFileName + " is invalid, allowed extensions are: " + _validFileExtension s.join(", "));
                    return false;
                }
            }
        }
    }
    return true;
} 
</script>
```
Example taken from [exploit-db](https://www.exploit-db.com/)

As you can see from the above code snippet, **JavaScript** processes the input before sending anything to the server and checks if your file has the extensions of the image file (jpg, jpeg, bmp, gif, png). This is easily bypassable by just uploading the image, and changing it's content and extension in the request using **Burp Suite**.


# Bypassing File name validation
File name validation is when the server backend checks the extension of uploaded file. This validation can be done with many methods but two of the most popular are **blacklisting** and **whitelisting**.

## Blacklisting

Blacklisting file extensions is a type of protection where certain file extensions are rejected. For example, the server might only reject **.php** extension but allows any other.

### Blacklisting bypass techniques
- Try other executable extensions
- Bypass case sensitive filter
- Idiotic Regex filter bypass
- Add shell to executable using .htaccess file

#### Try other executable extensions
PHP has multiple extensions and any of these will still work:
- pht
- phpt
- phtml
- php1
- php2
- php3
- php4
- php5
- php6
- php7

#### Bypass case sensitive filter
While uploading the shell, you can play with extension casing


Example code:

```php
if($imageFileType == "php") {
	echo "Only images are allowed.";
}
```

The above code snippet can be exploited by uploading non-popular php file extensions for example:


Or by simply changing the extension's cases such as:
- pHp
- Php
- phP
- any other combination of non-popular extensions

#### Regex filter bypass

Sometimes developers rely on **regex** to validate file extensions, and such cases might lead to a regex failure. If the backend checks for **.jpg** extensions in file name, but not at the end of it, it is vulnerable. Such cases can be bypassed with double extension like **shell.jpg.php**. Not quite common but worth testing for.

## Whitelisting

**Whitelisting** is the exact opposite of **blacklisting** - only allowing specified file extensions to be uploaded such as **jpeg**, **jpg**, **png**.

Example code:
```php
if($imageFileType != "jpg" && $imageFileType != "png" && $imageFileType != "jpeg" && $imageFileType != "gif" ) {
	echo "Only images are allowed.";
}
```

### Whitelisting bypass techniques
- Null byte injection
- Double extension bypass
- Invalid extension bypass

#### Null byte injection

Sometimes backend gets confused if there's **null byte** in the filename of uploaded file. In such cases, it can be exploited by uploading **shell.php%00.jpg** file which will be uploaded as **shell.php**.

#### Double extension bypass

Double extension bypass involves naming files such as **shell.php.png**, **shell.php;png** or **shell.php;png**. If you are extremely lucky, the result might be **RCE** but most likely it won't.

#### Invalid extension bypass

By submitting invalid extension, the underlying backend and OS could just ignore it and treat it as file name alone. For example, if we name the file **shell.php.lazar** it might completely ignore **lazar** extension because it doesn't exist, and save it as **shell.php**.