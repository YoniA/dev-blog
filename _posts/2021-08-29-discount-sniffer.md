---
layout: post
title:  "How I created a discount sniffer shell script that runs periodically and send email notifications"
date:   2021-08-29 20:40:47 +0200
categories: linux shell script curl sed grep
---


## The problem

I have a book whishlist in [Book Depository](https://www.bookdepository.com/), and I used to occasionaly visit the site and see if there are any special offeres and discount for some of my whishlist books.
This proccess is of course very inefficient, and maybe I missed some great deals on the days I didn't check the site.

There are several ways to automate this process.
For example, one could build a crawler using [Selenium](https://www.selenium.dev/) to navigate the site. But I decided to take the shell script approach as it is more
lightweight and does not involve the overhead of spawning a web browser for each sniffing session. In addition, the code is much shorter, using some standard tools like `curl`, `grep` and `sed`.

The process is outlined bellow.

## Getting page content

First, I made a text file containing all the urls of my books in the whislist. Each url in a separate line. For each url, I used [curl](https://curl.se/docs/manpage.html) to fetch the page content, then parse it and extract the book name and the amount of discount offered on this title (if any).


## parsing the URL line
I have noticed that each book has a fixed url format, like so:
`https://www.bookdepository.com/Classic-Shell-Scripting-Arnold-Robbins/9780596005955`. 

The middle part of the url contains the bookname and author(s) all separated by dash characters.
Since there is no separation between the book name and author, and a book can have seveal authors, I decided to consider the bookname as the whole string of bookname and author. In the example above, the bookname would be: `Classic-Shell-Scripting-Arnold-Robbins`.

In order to extract the bookname, I piped the url line into `sed` with the following substitution:
```bash
book_name=$(echo $url | sed 's/.*www\.bookdepository\.com\/\(.*\)\/.*/\1/')
```

Remember that the general replacement format in `sed` is `s/pattern/replacement/`, where `pattern` is a regular expression that matches a string in current line.
In our case, the `pattern` part is:
```
/.*www\.bookdepository\.com\/\(.*\)\/.*/`
```

which matches lines that contain any characters, followed by `www.bookdepository.com/something/anything`.

Notice that we have to escape slashes and dots (metacharacters) with backslashes.
 
The `something` part between the last two slashes is the bookname, enclosed inside a [capture group](https://www.regular-expressions.info/refcapture.html), which is denoted by the escaped opening and closing brackets `\(` and `\)`.

The `replcement part` is just `/\1/` which is the content of the first (and only) capture group from the `pattern` part. i.e, the bookname.

The discount amount is parsed in a similar manner (using `grep` this time).


## Send email notifications

In order to send an email from the script, I prepared an email template in a text file, named `mail.txt`,  with the appropriate headers only:

The content of `mail.txt`:
```
From: "Mr. Foobar" <foo.bar@example.com>
To: "Mr. Baz" <baz@example.com>

Subject: Discount Sniffer Alert - Book Depository
```
we iterate over each url in a loop, and for each discount found, we append to this file the book name, the amount of discount and a link to the book page.

When we finished looping over all URLs and if we found at least one discount, then we are ready to send an email notification, using our `mail.txt`.

we used the `curl` command with the appropriate flags to do that:

```bash
curl --ssl-reqd \ --url 'smtps://smtp.gmail.com:465' \ --user 'foo.bar@example.com:Fak!Pa123' \ --mail-from 'foo.bar@example.com' \ --mail-rcpt 'baz@example.com' \ --upload-file mail.txt
```

## Gmail - allow less secure app access

One issue with Gmail is that it prevents this kind of login by default, for security reasons. Gmail considers this a login from third party app and blocks it.  
In order to allow it, we have to manually enter the Gmail account, and under "Settings" --> "Security",  turn on "Allow less secure apps". 


## Putting it all together

Overall, the script looks like this:

```bash
#!/bin/bash

DISCOUNT_THRESHOLD=30

echo -e "More than $DISCOUNT_THRESHOLD% discount detected on the following books:\n" >> mail.txt

while read url; do

count
tmp=$(curl $url | grep "[0-9][0-9]%.*off")
discount=$(echo $tmp | cut -c1-2)
book_name=$(echo $url | sed 's/.*www\.bookdepository\.com\/\(.*\)\/.*/\1/')

if [[ "$discount" -gt "$DISCOUNT_THRESHOLD" ]]; then
        echo "* $book_name ($discount$)" >> mail.txt
        echo "* Check it out at: $url" >> mail.txt
        echo "===================================" >> mail.txt
        ((count++))
fi

done < whishlist-urls.txt

if (( $count > 0 )); then
        # send email
        curl --ssl-reqd \
                --url 'smtps://smtp.gmail.com:465' \
                --user 'foo.bar@example.com:Fak!Pa123' \
                --mail-from 'foo.bar@example.com' \
                --mail-rcpt 'baz@example.com' \
                --upload-file mail.txt
fi

# clear mail body, except headers
sed -i '6,$d' mail.txt
```

Here I have replaced my actual credentials with fake ones.

Invoking the script manually (`./discout_sniffer.sh`) yields something like this:


<img src="{{ site.baseurl }}/assets/images/sniffer-example.jpeg" alt="sniffer example" width="370" height="405">

## Automate sniffing with cron job

Say we want to invoke the script every day at midnight. We can add this as a job in our `crontab` file, and specify its periodicity.

Remembar that each line in the `crontab` file has the following format:

```
 ┌───────────── minute (0 - 59)
 │ ┌───────────── hour (0 - 23)
 │ │ ┌───────────── day of the month (1 - 31)
 │ │ │ ┌───────────── month (1 - 12)
 │ │ │ │ ┌───────────── day of the week (0 - 6) 
 │ │ │ │ │                                   
 │ │ │ │ │
 │ │ │ │ │
 * * * * * <command to execute>
```
(source: wikipedia)


We can open our `crontab` file with `crontab -e` and add the following line:

``` 
0 0 * * * cd /home/yoni/whishlist-discount-sniffer && ./discount-sniffer.sh
```

here `*` denotes all values in the range. So, our line tells the fllowing: invoke the script at 00:00, every day of month, every month, every day of week. Or equivalently, every day at midnight.

Notice that we first have to `cd` into the project directory, so the local invocation of the script succeeds.


See the [github repo](https://github.com/YoniA/various-shell-scripts/tree/master/whishlist-discount-sniffer) for this sniffer.
