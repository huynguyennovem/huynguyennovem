---
title: "Publish a landing page with a custom domain for your product"
datePublished: Fri Nov 21 2025 05:33:20 GMT+0000 (Coordinated Universal Time)
cuid: cmi8fcjeo000102l76hl4ak31
slug: publish-a-landing-page-with-a-custom-domain-for-your-product
tags: landing-page

---

In this article, I will use GitHub page (github.io) as it’s free and a custom domain purchased from [Namecheap](http://namecheap.com)

1. Purchase your favourite domain in [Namecheap](http://namecheap.com)
    
2. Create a GitHub account, then create a repo named `your-account-name.github.io`, it will build a page automatically.
    
3. Build your landing page with any web framework and push to the repository `your-account-name.github.io`
    
4. Link domain with github page:
    

* Go to your domain panel&gt; Advance DNS &gt; Add 4 `A record`(s) with IP as below with Host as `@`:
    
    * 185.199.108.153
        
    * 185.199.109.153
        
    * 185.199.110.153
        
    * 185.199.111.153
        
    
    And a `CNAME record` with Host as `www` and value as `your-account-name.github.io`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1763702776388/c8a4afd6-6d21-4a13-af5b-5f9193c1ce77.png align="center")

Go to the repo `your-account-name.github.io` &gt; Settings &gt; Pages , fill your custom domain and wait DNS check to be successful! Voila! You can also check `Enforce HTTPS` . GitHub page will auto-provide TLS/SSL to your site.