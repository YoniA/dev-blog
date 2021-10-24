---
layout: post
title:  "How I created a discount sniffer shell script that runs periodically and send email notifications"
date:   2021-08-29 20:40:47 +0200
categories: linux shell script curl sed grep
---


## The problem

I have a book whishlist in Book Depository, and I used to occasionaly visit the site and see if there are any special offeres and discount for some of my whishlist books.
This proccess is of course very inefficient, and maybe I missed some great deals on the days I missed.

There are several ways to automate this process.
First, one could build a crawler using Selenium to navigate the site. But I decided to take the shell script approach as it is more
lightweigth and does not involve spawning a web browser. Besides, it was a nice challenge with a good learning process.

This approach involves several aspects detailed bellow:

## Getting page content

First, I made a text file containing all the urls of my books in the whislist. Each url in a separate line. For each url, I used `curl` to fetch the page content.

Having the page downloaded, I had to parse it using `sed` to look for the discount element and extract the amount number.
In addition, I used `sed` to extract the book name for the url (see details bellow).


## parsing the URL line
I have noticed that each book has a fixed url format, like so:
`https://www.bookdepository.com/Classic-Shell-Scripting-Arnold-Robbins/9780596005955`. 

The middle part of the url contains the bookname and author(s) all separated by dash characters.
since there is no separation between the book name and author, and a book can have seveal authors, I decided to consider the bookname as the whole string of bookname and authror. In the example above, the bookname would be: `Classic-Shell-Scripting-Arnold-Robbins`.

In order to extract this from the URL line I used the following command:
```bash
book_name=$(echo $url | sed 's/.*www\.bookdepository\.com\/\(.*\)\/.*/\1/')
```
the URL line is pipe into `sed`, which matches the whole line, and the book name part in a catch-group, and substitute (replace) the whole line with the catch group.

remember that the general replacement format in `sed` is `s/pattern/replacement/`

in our case. the `pattern` part is:
`/.*www\.bookdepository\.com\/\(.*\)\/.*/`


match lines that contain any characters, followed by `www.bookdepositor.com/something/anything`

notice that we have to escape slashes and dots with backslashes.
 
  the `something` part between the last two slashes is the bookname, and is enclosed by escaped opening and closing brackets `\(` and `\)`, which denotes a catch group.

the `replcement part` is just `/\1/` which is the content of the first (and only) catch group from the `pattern ` part. i.e, the bookname.

The discount amount is parsed in a similar manner (using `grep` this time).


## Send email notifications

In order to send an email from the script,  i prepared an email template text file with the appropriate headers only:

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

One issue with Gmail is that it prevents this kind of login by default, for security reasons. Gmail considers this kind of login a login from third party app and blocks it.  

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


<img src="assets/images/sniffer-example.jpeg" alt="sniffer example" width="400", height="600">
![sniffer example](assets/images/sniffer-example.jpeg)

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


We can open our `crontab` file with `crontab -e` and add the following line:

``` bash
0 0 * * * cd /home/yoni/whishlist-discount-sniffer && ./discount-sniffer.sh
```

here `*` denotes all values in the range. So, our line tells the fllowing: invoke the script at 00:00, every day of month, every month, every day of week. Or equivalently, every day at midnight.

Notice that we first have to `cd` into the project directory, so the local invocation of the script succeeds.

