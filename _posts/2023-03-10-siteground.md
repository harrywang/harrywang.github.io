---
layout: post
title: Setup Websites using SiteGround
tags: tutorial website
permalink: sg
---

<img class="mx-auto" src="https://github.com/harrywang/harrywang.github.io/assets/595772/2bbc6a6e-e817-466e-8bab-7b034a8da364">

This post is mainly for myself - I write down the operations I often need to setup websites here to save time: I setup only a few websites a year and often forget what I did when trying to setup a new one.

## Setup Domain Name and Email

- Register domain on GoDaddy

- Create a website in SiteGround:

<img width="1563" src="https://github.com/harrywang/harrywang.github.io/assets/595772/69be6771-202c-4b91-942d-e15ddef22978">

- Create empty site and finish:

<img width="1512" src="https://github.com/harrywang/harrywang.github.io/assets/595772/a0396b92-3b91-4bef-a4e2-7187cd864765">

- Change the name servers in GoDaddy to use SiteGround for DNS:

```
ns1.siteground.net

ns2.siteground.net
```

- Go to site tools of SiteGround to setup emails:

<img width="283" src="https://github.com/harrywang/harrywang.github.io/assets/595772/86ce1639-450d-4a07-a02c-11c6c0544963">


## Clone/Migrate a Wordpress Website

The current Wordpress website is at `2022.yourwebsite.com`, which you want to clone to `2023.yourwebsite.com`.

- Go to SiteGround and create the subdomain name `2023.yourwebsite.com`:

<img width="289" src="https://github.com/harrywang/harrywang.github.io/assets/595772/3dba7120-e42d-47ea-9c37-37e8fbc0abe4">

- Go to 2022 Wordpress Admin site and install SG Migrator plugin:

<img width="654" src="https://github.com/harrywang/harrywang.github.io/assets/595772/e3d0c10b-4155-4fdf-96a7-a4685fa3dec2">

- Go to 2023 SiteGround site and generate a migration token (Site Tools -> WordPress > Migrator):

<img width="2134" src="https://github.com/harrywang/harrywang.github.io/assets/595772/ea5db6aa-534b-4414-b9a9-90654d7b6009">

- Go back to 2022 Wordpress Admin site > open SG Migrator > paste the Token and start a clone/migration:

<img width="1326" src="https://github.com/harrywang/harrywang.github.io/assets/595772/f6e7aafd-1241-4ba7-af23-3cc9f1a2e10a">

- (Optional) Go to 2023 SiteGround site and flush the new 2023 Dynamic Cache: Site Tools > Speed > Caching > Dynamic Cache

<img width="1840" src="https://github.com/harrywang/harrywang.github.io/assets/595772/29f647a5-6f8e-4b3a-aa7d-efc08d92cbc1">


PS. The featured image for this post is generated using Stable Diffusion, whose full parameters with model link can be found at [Takin.AI](https://takin.ai/asset/649b149dd1038ebe1e22af2b).
