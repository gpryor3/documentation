---
type: recipe
directory: third-party-integrations
title: Sailthru Integration
page_title: Automatically convert your email links into multi-platform deep links.
description: Add powerful, best in class deep linking to your email campaigns.
keywords: Contextual Deep Linking, Deep links, Deeplinks, Deep Linking, Deeplinking, Deferred Deep Linking, Deferred Deeplinking, Google App Indexing, Google App Invites, Apple Universal Links, Apple Spotlight Search, Facebook App Links, AppLinks, Deepviews, Deep views, Deep Linked Email, Sailthru
hide_platform_selector: true
sections:
- overview
- guide
- advanced
- support
---

{% if page.overview %}

Deep Linked Email allows you to automatically convert your email links into multi-platform deep links that take users directly to content in the app on mobile devices, while still maintaining the same web experience for desktop and mobile users without the app.

{% image src="/img/pages/third-party-integrations/responsys/deep-linked-email.png" center full alt='With and Without Branch Deep Linked Email' %}

With a script provided by Branch, you can dynamically create Branch links in email. In any place the script is called, the web URL is converted into its corresponding Branch link. The email is then sent.

When a link is clicked by a user without the app, it will route that user to the original web URL (including on desktop). When a link is clicked by a user with your app, it will direct that user into the relevant in-app content regardless of platform or email client.

{% getstarted %}{% endgetstarted %}

{% elsif page.guide %}

{% prerequisite %}
- This guide requires you to have already [integrated the Branch SDK]({{base.url}}/getting-started/sdk-integration-guide) into your app.
{% endprerequisite %}

## One time setup

### Enable remote deep linking functionality 

Contact your Branch Account Manager or [accounts@branch.io](mailto:accounts@branch.io) to enable remote deep linking functionality. 

{% protip %}
After your Branch Account Manager enables remote deep linking functionality, the remote configuration page will be unlocked. On this page, you or your Branch Account Manager can input settings to control a custom deep link remotely. 
{% endprotip %} 

## On-going use

Once you’ve completed the one time setup steps, it’s time to send your first email. 

Branch’s script will convert your web URLs to deep links. Simply identify the links you want to deep link with Branch and your Branch Account Manager will help you get set up.

You will need add a little bit of code in two places:

1. At the top of an email template
1. Immediately before a hyperlink

### Prepare your template

At the top of each email template, you should specify branch_base_url and branch_hash_secret, both of which are provided by the Branch account manager. 

Copy the below snippet and paste it above the `<head>` tag:

~~~~
{branch_base_url='BASE URL FROM BRANCH'}{branch_hash_secret='HASH SECRET FROM BRANCH'}
~~~~

Enter the base url and hash secret provided by your Branch account manager. 

{% example %}
~~~~
{branch_base_url='http://bnc.lt/abcd/3p?%243p=st'}{branch_hash_secret='fake secret'}
~~~~
{% endexample %}


### Create deep links 
Before each hyperlink, you’ll need to include a short amount of code. Put the original link (which will automatically be converted to a deep link) on the first line of the code snippet.

Before:

~~~~
<a href="ORIGINAL URL">Click me</a>
~~~~

After:

~~~~
{link='ORIGINAL URL'}

{*Branch deeplink builder*}{deeplink=branch_base_url + '&%24original_url=' + u(link)}{hash=md5(branch_hash_secret+deeplink+branch_hash_secret)}{deeplink=deeplink+'&%24hash='+hash}{*end Branch deeplink builder*}

<a href="{deeplink}">Click me</a>
~~~~

{% example %}
~~~~
{link='http://example.com/?utm=y'}

{*Branch deeplink builder*}{deeplink=branch_base_url + '&%24original_url=' + u(link)}{hash=md5(branch_hash_secret+deeplink+branch_hash_secret)}{deeplink=deeplink+'&%24hash='+hash}{*end Branch deeplink builder*}

<a href="{deeplink}">Click me</a>
~~~~
{% endexample %}


{% image src="/img/pages/third-party-integrations/sailthru/deep-linked-email-sailthru.png" center full alt='Deep Linked Email Sailthru Example' %}

{% protip title="Using Branch Links with Zephyr" %}
The Branch deep link script also works with Sailthru's Zephyr personalization language. Here's an example with the correct syntax.

~~~~
{link=content[0].url}

{*Branch deeplink builder*}{deeplink=branch_base_url + '&%24original_url=' + u(link)}{hash=md5(branch_hash_secret+deeplink+branch_hash_secret)}{deeplink=deeplink+'&%24hash='+hash}{*end Branch deeplink builder*}

<a href="{deeplink}">Click me</a>
~~~~

{% endprotip %}

{% caution %}
**Note:** You’ll need to do this for every URL you’re going to place in the email at this time. In the future we plan to handle this automatically.
{% endcaution %}

{% elsif page.advanced %}

## Setting up your link schema for email

The Branch script turns your web url (`ORIGINAL_URL` in the example snippet in this guide) into a Branch link. There are four ways to do this:

If you use your web URL as a deep link value:

1. **URL path:** If you use the path of your web URL as your  `$deeplink_path` value, or any other deep link value, then the configuration will automatically take the path of the URL and put it in deep link data.
1. **Full URL:** If you use the full web URL as your `$deeplink_path` value, or any other deep link value, then the configuration will take the entire URL and put it in deep link data.

If you use unique key/value data as deep link values:

1. **Hosted deep link data:** You can host your deep link data on your website with a metatag that looks like this `<meta name="branch:deeplink:my_key" content="my_value" />` where `my_key` and `my_value` will become a key value pair in deep link data. For each web URL, Branch will look for those tags and embed the deep link data (if found) into the deep link.
1. **As query parameters:** Simply append query parameters on to your web url and Branch will take those parameters and put them in deep link data.

## Universal Links

Sailthru will host an Apple App Site Association (AASA) file for you, so that your click tracking domain appears to Apple as a Universal Link, and the app will open and deep link.

To set up your AASA file, obtain the AASA file from your Branch account manager, and follow the [instructions provided by Sailthru](https://getstarted.sailthru.com/mobile/apple-ios-app-universal-links/) for setting up the HTTPS certificates. 

{% protip title="How does it work?"%}
Apple recognizes the click tracking domain as a Universal Link, and opens the app immediately without the browser opening. Once the app has opened, Branch will collect the referring URL that opened the app (at this time, it will be the click tracking url). Inside the app, Branch will robotically “click” the link, registering the click with the ESP, and returning the Branch link information to the Branch SDK inside the app. This information is then used to deep link the user to the correct in-app content. 
{% endprotip %}

{% image src="/img/pages/third-party-integrations/responsys/deep-linked-email-post-click.png" center full alt='Deep Linked Email Post-Click Flow' %}


### Redirect behavior and tracking

When your customer clicks the click tracking link in an email, the browser will generally open. Once in the browser, the click tracking redirect will happen, followed by an instant redirect to the Branch link. At this point, Branch will either stay in the browser, and load the original URL (if the app is not installed, or the customer is on a desktop device), or Branch will open the app and deep link to content. Branch uses the information from the original URL to deep link to the correct in-app content. 

{% elsif page.support %}

## How does link creation work?

### Three stages of a link

| Link name| Link example | Link description 
| --- | --- | --- | --- 
| Original link | https://www.shop.com/product | This is the original link that you would put in an email. If emails are dynamically personalized, this will be the link that is filled in by the personalization engine.  
| Branch link | https://branch.shop.com/?original_url=https%3A%2F%2Fwww.shop.com%2Fproduct | A Branch deep link, that handles all redirection for users on any platform, with or without the app.
| Click Tracking URL | https://email.shop.com/click/abcde12345 | A Sailthru generated click tracking URL. The URL doesn’t signify anything, but when clicked, records the click and redirects to a given destination.

{% image src="/img/pages/third-party-integrations/responsys/deep-linked-email-creation-flow.png" center full alt='Deep Linked Email Creation Flow' %}

## Styling
Your `<a>` tags can be styled however your email links are usually styled. In fact, you don’t need a `<a>` tag at all -- you can just use {deeplink} anywhere you would have used the original URL.

## Universal Links and click tracking
For Universal Links to work, Apple requires that a file called an “Apple-App-Site-Association” (AASA) file must be hosted on the domain of the link in question. When the link is clicked, Apple will check for the presence of this file to decide whether or not to open the app. All Branch links are Universal Links, because we will host this file securely on your Branch link domain.

When you click a Branch link directly from an email inside the Mail app on iOS 9+, it functions as a Universal Link - it redirects directly into the desired app. However, if you put a Branch Universal Link behind a click tracking URL, it won’t deep link into the app. This is because generally, a click tracking URL is not a Universal Link. If you’re not hosting that AASA file on the click tracking URL’s domain, you aren’t going to get Universal Link behavior for that link.


## Coming soon: “Don’t deep link”
In some cases you may have content on web that isn’t in the app - for example, a temporary Mother’s Day promotion. In this case, ideally you would be able to specify in the email that that link should not deep link. Using an alternate domain or path would be the best solution to arrive at this desired behavior, as Universal Links will not parse individual URLs for information without changes being made in the AASA file that specify unique paths. 

We're working with Sailthru to identify a solution for this.

{% endif %}