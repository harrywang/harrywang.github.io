---
layout: post
title: Headless Wordpress + Next.js + ElasticSearch
tags: tutorial website
permalink: wp
---

<img class="mx-auto" src="https://github.com/harrywang/harrywang.github.io/assets/595772/9247e59a-49e6-4c90-a2d9-423670a63405">

This past May 27th marks the [20th Anniversary of Wordpress](https://wp20.wordpress.net/). I have been using Wordpress for more than 15 years, e.g., I adopted Wordpress for [CSWIM](https://cswimworkshop.org/) in 2012 as a conference co-chair and Wordpress has been powering many websites I built. 

Recently, we needed to integrate a CMS with our main site at [Takin.AI](https://takin.ai/) and chose to use a Headless Wordpress after looking into many other options, such as [Strapi](https://strapi.io/), [Sanity](https://www.sanity.io/), etc. The main reason to choose Wordpress is because I know Wordpress very well and we have a Wordpress hosting plan on [SiteGround](https://www.siteground.com/) for years. 

## Basic Setup

We are quite happy with the integration shown at [Takin.AI Learning Center](https://takin.ai/learn) and the key steps for this implementation is as follows:

- Get a vanilla Wordpress working on SiteGround
- Install [WP GraphQL](https://www.wpgraphql.com/) plugin to serve the contents via API
- Install [WP Githuber MD](https://wordpress.org/plugins/wp-githuber-md/) plugin to author contents using Markdown
- Develop the frontend using Next.js and host it on [Vercel](https://vercel.com/)
- Redirect the WP frontend using a [custom theme](https://github.com/harrywang/redirect-headless-wp) I developed and keep `/wp-admin` only for content authoring
- Optimize the performance by installing [WP GraphQL Smart Cache](https://wordpress.org/plugins/wpgraphql-smart-cache/) and other methods

## ElasticSearch via ElasticPress Plugin

Follow the steps below to add search using ElasticPress Plugin and ElasticSearch (ES)

- Change the default ES username and password:

<img width="487" src="https://github.com/harrywang/harrywang.github.io/assets/595772/f1bc63b3-63f8-4429-91f7-bcd73410091d">

- add the username/pwd by adding one line `define( 'ES_SHIELD', '<username>:<password>' );` to the `wp-config.php` file using SiteGround UI:

<img width="898" src="https://github.com/harrywang/harrywang.github.io/assets/595772/e32d2ab1-5115-4250-98a5-2184e819705e">

- Check the connection - the warnings can be ignored:

<img width="1908" src="https://github.com/harrywang/harrywang.github.io/assets/595772/d3019f1f-062c-4155-88dd-20bb1315f2c2">

- Sync the posts information to the ES server:

<img width="1482" src="https://github.com/harrywang/harrywang.github.io/assets/595772/94b55de9-da8e-4895-a96b-4622a11e0ded">

- Check the index created in ES:

<img width="819" src="https://github.com/harrywang/harrywang.github.io/assets/595772/0275ab96-9f6b-4adb-aa5c-02593b039f2b">

- Check the contents in ES:

<img width="2153" src="https://github.com/harrywang/harrywang.github.io/assets/595772/a118de27-8c51-4a07-b0d5-dc75e01339ea">

## References

- [Vercel Headless Wordpress Docs and Template](https://github.com/vercel/next.js/tree/canary/examples/cms-wordpress)
- [Using Headless WordPress with Next.js and Vercel](https://vercel.com/guides/wordpress-with-vercel)
- [How to get related post by category in headless wordpress?(GraphQL)](https://stackoverflow.com/questions/73687998/how-to-get-related-post-by-category-in-headless-wordpressgraphql)
- [Use ElasticPress](https://github.com/10up/ElasticPress/issues/3141)

PS. The featured image for this post is generated using Stable Diffusion, whose full parameters with model link can be found at [Replicable.Art](https://replicable.art/asset/647b4d91414f1bb93778ab94).
