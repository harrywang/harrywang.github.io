---
layout: post
title: Headless Wordpress + Next.js
tags: tutorial coding 
permalink: wp
---

<img class="mx-auto" src="https://github.com/harrywang/harrywang.github.io/assets/595772/c3fbe3d1-6563-44ef-b06c-6ba9f0f0c275">

This past May 27th marks the [20th Anniversary of Wordpress](https://wp20.wordpress.net/). I have been using Wordpress for more than 15 years, e.g., I adopted Wordpress for [CSWIM](https://cswimworkshop.org/) in 2012 as a conference co-chair and Wordpress has been powering many websites I built. 

Recently, we needed to integrate a CMS with our main site at [Replicable.Art](https://replicable.art/) and chose to use a Headless Wordpress after looking into many other options, such as [Strapi](https://strapi.io/), [Sanity](https://www.sanity.io/), etc. The main reason to choose Wordpress is because I know Wordpress very well and we have a Wordpress hosting plan on [SiteGround](https://www.siteground.com/) for years. 

We are quite happy with the integration shown at [Replicable.Art Learning Center](https://replicable.art/learn) and the key steps for this implementation is as follows:

- Get a vanilla Wordpress working on SiteGround
- Install [WP GraphQL](https://www.wpgraphql.com/) plugin to serve the contents via API
- Install [WP Githuber MD](https://wordpress.org/plugins/wp-githuber-md/) plugin to author contents using Markdown
- Develop the frontend using Next.js and host it on [Vercel](https://vercel.com/)
- Redirect the WP frontend using a [custom theme](https://github.com/harrywang/redirect-headless-wp) I developed and keep `/wp-admin` only for content authoring
- Optimize the performance by installing [WP GraphQL Smart Cache](https://wordpress.org/plugins/wpgraphql-smart-cache/) and other methods

I list some references we used:

- [Vercel Headless Wordpress Docs and Template](https://github.com/vercel/next.js/tree/canary/examples/cms-wordpress)
- [Using Headless WordPress with Next.js and Vercel](https://vercel.com/guides/wordpress-with-vercel)
- [How to get related post by category in headless wordpress?(GraphQL)](https://stackoverflow.com/questions/73687998/how-to-get-related-post-by-category-in-headless-wordpressgraphql)

PS. The first image for this post is generated using Stable Diffusion, whose full parameters with model link can be found at [Replicable.Art](https://replicable.art/asset/64301d2e38888d0da5220944).
