![Cover](Images/image-ZSGOJ0HZ.jpg)![Cover](Images/image-ZSGOJ0HZ.jpg)

#### Andrea M. Fuller

### The SEO Checklist for Developers

Copyright © 2023 by Andrea M. Fuller

All rights reserved. No part of this publication may be reproduced, stored or transmitted in any form or by any means, electronic, mechanical, photocopying, recording, scanning, or otherwise without written permission from the publisher. It is illegal to copy this book, post it to a website, or distribute it by any other means without permission.

First edition

This book was professionally typeset on Reedsy

Find out more at reedsy.com

## Contents

[I. PART ONE](#chapter0001.html)

[1\. Intro](#chapter0002.html)

[2\. Getting Started](#chapter0003.html)

[3\. Technical SEO](#chapter0004.html)

[4\. Onsite + Content SEO](#chapter0005.html)

[5\. Conclusion](#chapter0006.html)

[II. PART TWO](#chapter0007.html)

[6\. Bonus: Developer’s Guide to SEO-Friendly Pagination: Tips, Tricks, and Traps](#chapter0008.html)

[7\. So, what exactly is pagination?](#chapter0009.html)

[8\. What’s the deal with Infinite Scroll?](#chapter0010.html)

[9\. A Developer’s Guide to Pagination Best Practices](#chapter0011.html)

[10\. A Developer’s Guide to Handling Infinite Scroll](#chapter0012.html)

[11\. Common Pagination Mistakes to Avoid](#chapter0013.html)

[12\. In a Nutshell](#chapter0014.html)

[About the Author](#chapter0015.html)

[Also by Andrea M. Fuller](#chapter0016.html)

I

Part One

## 1

### Intro

We know that as a developer, you’re a master of crafting digital wonders, but have you ever wondered how to make sure your creations get noticed by search engines and the right audience? That’s where this friendly SEO checklist comes to the rescue!

Think of this checklist as your trusty sidekick, your SEO compass in the labyrinth of algorithms and keywords. It’s here to break down the complex world of SEO into bite-sized, developer-centric chunks.

Whether you’re a seasoned developer or just getting started, this checklist is designed to be your go-to resource.

## 2

### Getting Started

Set up Google Search Console (GSC) and Bing Webmaster Tools: Get these tools up and running to monitor your site’s health and performance in search engines.

Set up Google Analytics (GA): Understand your site’s traffic and user behavior by setting up Google Analytics.

Install and Configure SEO Plugin (for WordPress): If you’re using WordPress, choose and set up an SEO plugin to optimize your site’s SEO easily. (Yoast Pro is the best because it allows you to add 301 redirects in it, have full control of archive pages and configure basic schema code.)

Generate and Submit Sitemap: Submit the sitemap to GSC and Bing to help search engines navigate your site effectively once the site has been pushed live. Yoast Pro will generate the sitemap for you and continue to automatically update it.

Create Robots.txt: Craft a robots.txt file to guide search engine bots on which parts of your site to crawl and which to avoid.

Check for Manual Actions: Regularly review Webmaster Tools for any manual actions that might affect your site’s search presence.

Ensure Site Indexing: Confirm that search engines are indexing your site’s pages properly. Check Day 2 post-launch and weekly after that for 3 months. After that review monthly with metrics report and 404 check, etc.

## 3

### Technical SEO

Ensure HTTPS Usage: Secure your site by using HTTPS protocol.

Check for Duplicate Versions: Avoid duplicate content issues by ensuring only one version of your site is accessible. These can occur with Archive pages, tags (which you should never use), and collections pages or product pages on ecommerce sites. Also, some CMSs automatically create them because the canonical is not automatically set for product pages.

Fix Crawl Errors: Identify and resolve any issues that might hinder search engine crawling.

Speed Up Your Site: Improve your site’s loading speed for better user experience and search rankings. Use Google’s free Page Speed Insights checker. Make sure to use a CDN to cache the site to help.

Link Checkup: Regularly scan for broken internal and external links.

HTTP Links on HTTPS Pages: Update any HTTP links found on your HTTPS pages.

SEO-Friendly URLs: Craft URLs that are easily understandable by both users and search engines. Make sure to set the permalinks to include the page hierarchy within the URL and the blog name/category/post-title in the blog URLs.

Add Schema Markup: Enhance your site’s visibility in search results with schema markup. Can be done through Yoast for basic schema, if your site is ecommerce or needs more than the basics then create, test and deploy.

Assess Page Depth: Keep an eye on how many clicks it takes to reach different pages on your site.

Redirect Check: Ensure your redirects (301s) are set up correctly. Do NOT use 302s and make sure to add 410s to any URLs you fully delete. 301s are just a “change of address form” not to be used for deleted content.

Add a Security Plugin: Activate a great security plugin or app (depending upon the CMS) to help with brute force attacks, scrapping, etc.,

## 4

### Onsite + Content SEO

H1 Tag Cleanup: Avoid multiple H1 tags on a single page to maintain a clear hierarchy. Only the page title should have a H1 tag.

H2 Tag Set-up: Make sure that all paragraph headers are set to H2 and properly styled in the CSS. Search engines need to see H2 tags to gain context and want to see the keyword present in them.

Image Size and File Name: Compress and size properly all images before uploading. Ensure the file name includes the company/organization’s name and a generic description of what it is (adding the keyword for that page is a bonus). Ex: companyx_aboutus_ceo_janedoe.jpeg

Image Alt Tags: Add descriptive alt tags to images for accessibility and SEO benefits (adding the keyword the page is optimized for is a bonus).

Internal Linking: Strategically link between your site’s pages to improve navigation and SEO.

Pagination & Canonical Tags: Ensure these are properly set and applied across the site. Each CMS is slightly different so, check to see they are there and working as they should (Ex: Shopify automatically adds the canonical tag, but doesn’t add the right URL, so you need to update the rule in the Liquid file.)

## 5

### Conclusion

And there you have it my developer friends! You’ve just powered through our SEO checklist like a coding maestro. Congrats!

If you ever find yourself lost in the SEO maze or need a friendly nudge in the right direction, remember that this checklist is here to guide you.

Keep coding, keep optimizing, and keep being the awesome developer you are. Cheers to your website’s success and all the great things yet to come!

II

Part Two

## 6

Bonus: Developer’s Guide to SEO-Friendly Pagination: Tips, Tricks, and Traps

Hey there, my trusty developer friends! In this bonus section we’re diving into the intriguing world of pagination.

You might wonder why this topic is crucial for us in the development realm, so let’s break it down and talk about:

What pagination is,

Its impact on SEO, and

Why it’s vital to get it right

## 7

### So, what exactly is pagination?

It’s the practice of dividing your website’s content into multiple pages. You’ve seen it on news sites, blogs, forums, and almost everywhere in the e-commerce universe.

![Image](Images/image-PLLT4UBU.png)

Pagination is like the bread and butter of website content distribution, but it’s not all sunshine and rainbows.

Getting pagination right is crucial for SEO.

Here’s the scoop:

The quality of your pagination setup can make or break how search engines index your pages and the content they contain — links, articles, products, and more.

Let’s take a look at an example of pagination. In this example:

The series is titled “Awesome Recipes Series” and has a URL and an image associated with it.

It includes two pages, “Page 1” and “Page 2,” each with its URL and name.

You can add more pages by duplicating the structure and incrementing the position and content attributes in the meta tags.

The pagination metadata specifies that there are 10 pages, starting from page 1, ending at page 10, and the current page is 1.

<div itemscope itemtype=“http://schema.org/Series”>

<span itemprop=“name”>Awesome Recipes Series</span>

<link itemprop=“url” href=“https://example.com/awesome-recipes”>

<link itemprop=“image” href=“https://example.com/awesome-recipes-thumbnail.jpg”>

<! - Page 1 →

<div itemprop=“hasPart” itemscope itemtype=“http://schema.org/WebPage”>

<a itemprop=“url” href=“https://example.com/awesome-recipes/page/1”>

<span itemprop=“name”>Awesome Recipes - Page 1</span>

</a>

<meta itemprop=“position” content=“1”>

</div>

<! - Page 2 →

<div itemprop=“hasPart” itemscope itemtype=“http://schema.org/WebPage”>

<a itemprop=“url” href=“https://example.com/awesome-recipes/page/2”>

<span itemprop=“name”>Awesome Recipes - Page 2</span>

</a>

<meta itemprop=“position” content=“2”>

</div>

<! - Add more pages here →

<! - Pagination metadata →

<meta itemprop=“numberOfPages” content=“10”>

<meta itemprop=“pageStart” content=“1”>

<meta itemprop=“pageEnd” content=“10”>

<meta itemprop=“currentPage” content=“1”>

</div>

## 8

### What’s the deal with Infinite Scroll?

It’s the polar opposite of pagination. Instead of chopping content into separate pages, infinite scroll crams it all onto one page, letting users scroll indefinitely.

![Image](Images/image-14WEIAUL.png)

Sometimes, you’ll see a “Load more” button to reveal the next batch of content, but technically, it’s still one page.

Search engines perceive both pagination and infinite scroll as a single page.

Infinite Scroll has its perks, like a sleek look and mobile-friendliness, but from an SEO standpoint, it’s not a silver bullet.

Here’s the rub: Googlebot can’t mimic scrolling behavior and definitely can’t click “Load more.” So, search engines struggle to effectively evaluate and index all the content on an infinitely scrolling page.

Pagination doesn’t have these issues because crawlers treat each page as a distinct entity.

## 9

### A Developer’s Guide to Pagination Best Practices

Now, let’s talk about best practices for pagination straight from Google’s playbook:

1\. Sequence of Links to Pages

Always include a link to the next page using the <a href> tag on each page.

Also, ensure a link to the first page within the pagination on every page so search engines favour your target result in SERP.

2\. Create the Correct URL

Use unique URLs with the parameter ?page=n to show the current page number in the URL.

Avoid referring to the first page as canonical; use canonical tags with standalone links for each page.

3\. Data Structure

Employ micro-markup to indicate to search engines that your site uses pagination. Schema.org is your friend here.

4\. Avoid Duplicate Text

If you have introductory text in your category during pagination, put it on the first page.

5\. Say No to URL Fragment Identifiers

Google ignores fragment identifiers (#) in URLs. Make sure your links are crawlable.

6\. Rel=”next” and Rel=”prev” Tags

Google no longer requires these tags, introduced way back in 2011. Micro-markup is enough.

Bonus Tips

Want to supercharge your pagination? Consider these extras:

Use preload, reconnect, or prefetch tags to speed up page transitions for users.

There are two visual pagination types: “Next/Previous” and “Page numbering.” Choose the one that suits your content volume best.

## 10

### A Developer’s Guide to Handling Infinite Scroll

If you’re leaning towards infinite scroll, remember it’s often powered by AJAX technology.

Search engines may miss some content since it’s hidden until users scroll or click to load more.

Here’s a JavaScript snippet to give you an idea:

window.onscroll = function() {

// Check if the user has scrolled to the bottom of the page

if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {

// You're at the bottom of the page, load more content here.

// This is where you would make an AJAX request to fetch additional content.

// Once the new content is loaded, you can append it to the existing content on the page.

// Don't forget to implement error handling and loading indicators if needed.

}

};

In this code:

window.onscroll is an event listener that triggers the function when the user scrolls.

window.innerHeight represents the height of the browser’s viewport.

window.scrollY indicates how much the page has been scrolled vertically.

document.body.offsetHeight gives you the total height of the document.

When the sum of window.innerHeight and window.scrollY equals or exceeds document.body.offsetHeight, itmeans the user has scrolled to the bottom of the page, and you can trigger the loading of additional content.

Remember to replace the comment “// Load more content here” with the actual code to load and append new content to your webpage.

Additionally, consider adding debounce or throttle mechanisms to avoid excessive calls to the scroll event and improve performance.

In addition, we need to talk about breaking the content into manageable sections. This ensures accessibility even when JavaScript is disabled and maintains the URL integrity.

Here’s a breakdown:

1\. Divide Content for Accessibility

When implementing infinite scroll, it’s crucial to divide your content into smaller, accessible sections. This ensures that users with JavaScript disabled can still access and navigate your content seamlessly. Avoid duplicating these sections. Consider these examples:

Example 1: site.com/stickers&page=1

This URL structure indicates the first content page within the “stickers” category.

Example 2: site.com/stickers?lastid=123

Using a unique identifier like “lastid” helps maintain URL uniqueness, allowing users to access specific sections directly.

Example: site.com/stickers#1

While using aURL fragment identifier (#) isn’t ideal for SEO, it still provides some accessibility. However, using query parameters or clean URLs whenever possible is better.

2\. Ensure URL Uniqueness and Functionality

Your URLs should be both unique and functional. They should work independently of user cookies or browsing history. This guarantees a smooth user experience and proper indexing by search engines.

Examples are crucial here:

Unique and Functional URL: site.com/stickers&page=2

This URL references the second page of content in the “stickers” category, and it’s both unique and functional.

Avoid URL Fragment Identifiers:

Don’t rely solely on fragment identifiers like site.com/stickers#1, as they may not be as reliable for functionality and indexing.

3\. Enhance User Experience with replaceState and pushState

Consider using replaceState and pushState to further improve the user experience, depending on user behaviour on your website:

replaceState: Use this method when user actions, like clicking or scrolling, are similar to mouse clicks. It replaces the current URL in the browser’s history without creating a new entry.

pushState: Employ this method when you want users to be able to return to previously scrolled content easily. It adds a new entry to the browser’s history.

Here’s a quick example:

// Using pushState

history.pushState({ page: 'scroll' }, 'Scrolled Content', '/teams/giraffes?page=2');

// Using replaceState

history.replaceState({ page: 'scroll' }, 'Scrolled Content', 'teams/giraffes?page=3');

By utilizing these methods strategically, you can provide a more seamless and user-friendly experience when implementing infinite scroll.

In summary, when optimizing infinite scroll, consider accessibility, URL structure, and user experience enhancements through state manipulation.

This approach ensures all users, including those with JavaScript disabled, can navigate your content effectively while improving SEO and user engagement.

## 11

### Common Pagination Mistakes to Avoid

Lastly, steer clear of these common pagination pitfalls:

Using the “noindex” tag instead of canonical tags can prevent search engines from indexing your pages correctly.

Static URLs for pagination are a no-go. Dynamic parameters in URLs are the way to go.

Ensure that pages not in the current category return a 404 error.

## 12

### In a Nutshell

The key issue with improper pagination is restricted access to your content. To dodge this pitfall, remember these golden rules:

Use user-friendly href links for pagination that crawlers can access.

Ensure only the first page in the sequence is optimized and remove any SEO-specific content from pagination URLs.

Remember that Googlebots can’t scroll or click, so ensure all your content is accessible even without JavaScript.

Double-check the accessibility of your page URLs in Google Search Console.

Go forth and paginate wisely. Your website’s SEO will thank you!

![photo of the author](Images/image-CY5D9XQU.jpg)

## About the Author

Andrea M. Fuller is a seasoned Digital Marketing and SEO expert with over 20 years of experience in multiple industries that have ranged from start-ups to global PR firms.

Andrea’s deep knowledge of technical platforms and passion for strategy allows her to create and execute unique and effective strategies for clients. As a fourth generation entrepreneur, she is passionate about helping businesses succeed by utilizing the online space to create genuine engagement between corporations/organizations and their clients/constituents.

She owns Fuller Digital Strategy, LLC - a full service Digital Marketing agency and SEO Handy Woman - a online academy teaching DIY website owners how to execute top-line SEO strategies themselves.

You can connect with me on:

https://fullerdigitalstrategy.com

https://www.seohandywoman.com

Subscribe to my newsletter:

https://fuller-digital-strategy-llc.ck.page/3b2de93afd

## Also by Andrea M. Fuller

![The NO B.S. Guide to Shopify SEO](Images/image-I717MFRG.png)

### The NO B.S. Guide to Shopify SEO

A must-read for any Shopify entrepreneur who wants to take their online store to the next level.

Written by renowned SEO expert and entrepreneur Andrea Fuller and sold in more than 5 countries, this guide distills the secrets of SEO that actually drive sales.

You’ll learn insider tactics and techniques that will help you optimize your store for search engines and boost your rankings, bringing in more traffic and sales.