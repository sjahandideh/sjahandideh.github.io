---
layout: post
title: "Development Tools and Tricks"
date:  "2015-10-02 00:00 UTC"
tags: Tools
---

##Purpose of this post##
Have you ever asked yourself, "How can that guy be so quick in solving
problems?". Well, I have asked that question many times! We often see
that as a sign of smartness but the truth is those people are just more
resourceful than you. The number and quality of the development and
debugging tools that we use, have a great effect on how we solve
problems. In this post, I'm going to take you through a number of cool
development tools that can make your life easier and help you create
better quality code and debug issues more quickly. The focus of this
post is not the common gems and tools we often use with Rails, but the
ones that we may miss. I'm going to categorise tools into the following:

  * Converting designs to html pages
  * Responsive design and window emulation
  * Debugging
  * API interaction
  * Database interactions
  * Formatting
  * Testing

##Convert UX design into a page##


####[Page Ruler](https://chrome.google.com/webstore/detail/page-ruler/jlpkojjdgbllmedoapgfodplfhcbnbpn?utm_source=chrome-app-launcher-info-dialog)####
A Google Chrome extension that Lets your draw out a ruler to any page and displays the width, height and position of it.

####[PerfectPixel](https://chrome.google.com/webstore/detail/perfectpixel-by-welldonec/dkaagdgjmgdmbnecmcefdhjekcoceebi?hl=en)####
A Google Chrome extension that allows developers to put a semi-transparent image overlay over the top of the developed HTML and perform per pixel comparison between them. It's very useful when you want to make sure the page you created has the exact dimensions as the design.

####[Image Tool](https://chrome.google.com/webstore/detail/image-tool/gnmkbnaemkbbeeddfmbmdlnglfdmhdej)####
A Google Chrome extension that allows you to select images and interpret them by using one of the super-useful tools:

Color picker - click on a color to know it's value and add it to your list. You can switch between RGB and HEX and copy the result to clipboard.

####[WhatFont](https://chrome.google.com/webstore/detail/whatfont/jabopobgcpjmedljpbcaablpmlmfcogm)####
A Google Chrome extension that allows you to inspect web fonts by just hovering on them. It is that simple and elegant.

##Responsive design and window emulation##


####[BrowserStack](https://www.browserstack.com/)####
An online application that enables live, web-based browser testing. You
will have instant access to all desktop and mobile browsers.

####Google Chrome > Inspect Element > Mobile Icon####
By clicking on the mobile icon, you will see a ruler on the page and a
list of devices. You can select different devises and see how your page
looks like in a different devise.

####[Window Resizer](https://chrome.google.com/webstore/detail/window-resizer/kkelicaakdanhinjdeammmilcgefonfh?hl=en)####
A Google Chrome extension that re-sizes the browser's window in order to emulate various resolutions. It is particularly useful for web designers and developers by helping them test their layouts on different browser resolutions.

##Debugging##


####[Web Developer](https://chrome.google.com/webstore/detail/web-developer/bfbameneiokkgbdmiekhjnmfkcnldhhm/related)####
This is an awesome Chrome extension. Although I included it in the
debugging category, it really does more than just helping with the
debugging. It is similar to Chrome developer tools. However, it provides a
whole lot of other cool things. 👍

####[AJAX Debugger](https://chrome.google.com/webstore/detail/ajax-debugger/lgfefckpdealogpcfjdhinecfbcgedam?hl=en)####
This Chrome extension logs all AJAX (XMLHttpRequest) activity to Chrome's Developer Tools Console. This allows developers to easily see top-level request info, like the HTTP status, response time, and size. It's a bit similar to looking at Network > XHR but it's more comprehensive and user-friendly. By clicking on the URL, it opens the AJAX call in a new tab displaying all the neccessary information and making debugging much easier. 👍

##API interactions##


####[Postman](https://www.getpostman.com/)####
An application that lets you construct requests quickly, save them for later use and analyze the responses sent by the API. Postman can dramatically cut down the time required to test and develop APIs. It also has a test editor that is not free. 👍

####[DHC - REST/HTTP API Client](https://chrome.google.com/webstore/detail/dhc-resthttp-api-client/aejoelaoggembcahagimdiliamlcdmfm)####
##Database interactions##

####[Chrome MySQL Admin](https://chrome.google.com/webstore/detail/chrome-mysql-admin/ndgnpnpakfcdjmpgmcaknimfgcldechn)####
It is similar to Sequel Pro. It provides the administration GUI of MySQL server.

##Formatting##


####[JSON Formatter](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa?hl=en)####
Opens a JSON file in a readable manner. 👍

##Testing##


####[Form Filler](https://chrome.google.com/webstore/detail/form-filler/bnjjngeaknajbdcgpfkgnonkmififhfo?hl=en)####
This Chrome extension allows you to fill all form inputs (textboxes, textareas, radio buttons, dropdowns, etc.) with random/dummy data. 👍

##Resources##

[handy chrome extensions and apps](http://tutorialzine.com/2015/06/35-handy-chrome-extensions-and-apps-for-developers/)
