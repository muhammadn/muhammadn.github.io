---
layout: default
title:  "Introducing API Vault"
date:   2019-03-22 20:31:00 +0800
---

I work mostly on APIs. Writing APIs on many languages and different ways of interacting with each other.

Currently at my work i wrote APIs in Ruby, PHP and Go. This is my current toolstack but sometimes i might use
Rust for background processing or Elixir when needed. Oh yeah, Python. My favourite tool for working with data.

For my work, we have many microservices and each has their *own* authentication endpoints. Which is bad and JWT Tokens
cannot be used in every of those microservices since the JWT Secret is different.

After looking around for solutions, in particular AuthN/AuthZ which i find to be really overkill and since i was looking
for simplicity, i took a look at API Gateways.

But almost all i saw was proprietry or opensource but-you-have-to-pay-if-you-use-it-commerically.

The API Gateway that almost fill my needs was [Express Gateway](https://www.express-gateway.io/). But the limiting part
was that they do not provide a SQL database connector. (MySQL in Particular)

So after reading [Ben Church's](https://github.com/bechurch) excellent [tutorial](https://hackernoon.com/writing-a-reverse-proxy-in-just-one-line-with-go-c1edfa78c84b) about Reverse Proxy in Go,
i modified parts of his code to suit my needs for an API Gateway.

I had talked to Ben Church to license my modifications as MIT and he agreed to it.

In summary, here is what it looks like:

![API Vault]({{ site.url }}/assets/images/API_gateway.png){:class="img-responsive"}

I call it "API Vault". So basically it does these things:

1. User will authenticate to API Vault.
2. API Vault returns a JWT Token.
3. User accesses a microservice via API Vault with their JWT Token.
4. API Vault "converts" the JWT Token to another Token for accessing the microservice endpoint
5. User gets data from their microservice endpoint

It is simple. 

What API Vault does not do:
1. Does not do authorization. This should be handled by your microservice, although
  in theory i can implement it. But different microservices might have different authorization rules.

So basically you need to authenticate to API Vault only *once* and access your services with just one token.

And here's another thing - your microservices can have different JWT Secrets and API Vault happily does the conversion for you.

Another thing - no changes are needed for your current microservices to use API Vault. Just configure API Vault
and API Vault will use the secrets from your microservices to generate it's token to the microservice.

API Vault supports MySQL, PostgreSQL and Microsoft SQL.

You can check out the Github Project [here](https://github.com/muhammadn/APIVault) (don't forget to star ⭐️ it!)
