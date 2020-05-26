---
layout: default
title:  "Disclosure of Celcom API Data Leaks"
date:   2020-05-26 19:56:01 +0800
---

So it's more than a month and as a Celcom Customer i had seen a lot of updates on my Celcom Life App. I had alerted them back then and i hope they had already taken steps to mitigate the security holes on their Celcom Life's API backend.

Back in April, My Celcom Life app was behaving strangely where when i reloaded my credit and tried to buy a data plan without refreshing my app.

Well, the request for my data plan  *did not* go through - which it should. Then i realised that the server is validating against my local Celcom Life app Credits and not the verification of the credits were not done at the server.

So i had suspected if the server relied on my phone's 'credit' information, there could be a possibility that anyone could tell the server "Hey!, i have RM100 credits, give me RM30 credits data plan for 1 month".

Out of curiousity, i ran my Android Emulator, setup a Man-in-the-Middle attack on the Celcom Life mobile App on the Emulator by making sure Celcom Life app trusts my "own Certificate"

Little did i expect that this would turn out that i would go down the rabbit hole and discover many other issues with Celcom Life's API security.

The first step was the Celcom Life App OTP 2FA, when you request for the 2FA token via SMS, the response from the API shockingly gave an internal server's "analytics" username and password.

![Celcom OTP]({{ site.url }}/assets/images/OTP_Celcom.png){:class="img-responsive"} 

After getting logged in with the OTP and playing around with the Celcom Life App on my Android Emulator, i got to a point that i could see my own *phone sim's* subscribed plans, available plans and my data usage.

Then i tried to change the parameters to query information about another random phone number by changing the last few digits (MSISDN number and that's what telco's call it) and got THEIR plan information as well, including the subscription plans, data usage and more.

![Celcom API]({{ site.url }}/assets/images/Celcom Life API.png){:class="img-responsive"}

There is an upstream server which is behind the Celcom Life App API server that is probably where the telco provisioning is done. This server serves data in XML which Celcom Life API parses the XML and serves in JSON. 

This security vulnerability has been reported to Celcom on the 5th April 2020 and they had made updates (from what i had seen because of their downtime) on the same week.

I did not get past checking if i can get free data plans but the exposure of customer's data was serious enough for me to forget about it (since i am a Celcom Customer). However, as much as a suspected that might be vulnerable i did not look into it further.

Celcom Life App handles transactions for credits, data plans, roaming, billing and more for PREPAID AND POSTPAID customers so i feel it is really serious headache to know someone who is on the same network (celcom) could gain access to your personal subscriber information.

Incident Timeline:

April 5 - Discovered Analytics and Subscriber data leaks

April 5 - Alerted Celcom

April 8 - Maintenance and Updates
