---
layout : default
title : Mail-Checker using Intel Galileo
title-img: images/2016/mail-checker/three.png
---

This was a project I did last year.As soon as I learnt about accessing GPIO in Galileo, I wanted to implement it somehow. So I decided to make my own Mail-Checker.

Target :  To switch LED on whenever a new mail is received and turn it off when all messages have been read.

This project is based on IMAPClient library in Python. The python script is executed on the Debian running Intel Galileo Gen 2 board.

GPIO Mapping : I am using `gpio38` of Galileo which is mapped to Digital Pin number 7 of Arduino.

IMAPClient installation:

```
wget http://freshfoo.com/projects/IMAPClient/IMAPClient-0.13.zip
unzip IMAPClient-0.13.zip

cd IMAPClient-0.13

python2.7 setup.py install
```
After installation , if there is some path error , you can type following in the terminal:

```
export PATH=$PATH:path/to/dir

in this case , path/to/dir is /home/IMAPClient-0.13
```
Now all you need is internet connectivity in Galileo and run the python code given at the end.

As you can see, after reading all the messages, the mail-checker shows ‘0’ messages.

![three]({{ site.baseurl }}/images/2016/mail-checker/three.png)

Also it should be noted that the script logs into the email account after every 60 seconds to keep the check on the emails.

![four]({{ site.baseurl }}/images/2016/mail-checker/four.png)

![five]({{ site.baseurl }}/images/2016/mail-checker/five.jpg)

That’s it. Mail-checker is ready for action!

Do check out the code on [github](https://github.com/ioarun/mail-checker-galileo) and if you like this post please do follow for more interesting articles.
