---
title: "Nginx Proxy Manager with Cloudflare Proxy and full SSL"
date: 2023-06-15T14:24:46+03:00
tags: ["nginx-proxy-manager","cloudflare"]
categories: ["sysadmin"]
draft: false
#description: "Desc Text."
#author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
UseHugoToc: true
hidemeta: false
comments: false
#canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
summary: "In this article I'll show you how to setup Cloudflare with nginx-proxy-manager so that we
can proxy connections through Cloudflare and have full SSL setup."
#hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowCodeCopyButtons: true
ShowRssButtonInSectionTermList: true
robotsNoIndex: false
#cover:
#    image: "<image path/url>" # image path/url
#    alt: "<alt text>" # alt text
#    caption: "<text>" # display caption under cover
#    relative: false # when using page bundles set this to true
#    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/tvlpirb/systuner.com/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
# weight: 1
# aliases: ["/first"]

---

In this article I'll show you how to setup Cloudflare with nginx-proxy-manager so that we
can proxy connections through Cloudflare and have full SSL setup.

## Pre-requisites
- Nginx Proxy Manager setup
- Cloudflare setup

## Steps:
1. Visit your Cloudflare dashboard and navigate to the domain which you'd like to use
2. On the side panel, under SSL/TLS, select the Origin Server option
3. Thereafter, click create a certificate and choose your desired settings. The
defaults should be fine for our purposes.
4. Save the Origin certificate to domain.pem and Private Key to domain.key. Make
sure to protect these or else someone can MITM your connection to Cloudflare.
5. On the side panel, under Overview section, select how you would like TLS to
behave. I have selected Full SSL option.
6. Navigate to Nginx Proxy Manager admin page, on the top menu select SSL certificates
7. On this page select "Add Certificate", make sure it is the button which is next
to the question mark box. Thereafter, select custom
8. For certificate key select domain.key file and for certificate select the domain.pem file
9. Now when adding a proxy, under SSL select our newly added certificate.

