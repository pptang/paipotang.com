---
title: Impersonate the service account instead of managing a new key
description: Impersonate the service acount instead of generating a new service account key and store it locally
date: 2021-05-24
tags:
  - TIL
layout: layouts/post.njk
---

Today I learned how I can impersonate the service account to access the resources/services in Google Cloud Platform without generating a new service account key and store it in my local machine for development purpose!

## How we do it normally
In order to access certain resource or service on the Google Cloud Platform, we normally go to the console, generate a new service account key and download it to our local machine. We'll pass the path to this service account key as an environment variable and it'll be consumed when we launch the service locally for development. It can be visualized as follows:

![store service account key locally](/img/posts/impersonate-service-account/store-service-account.svg)

### There are several issues with this approach:
- It's easy to mis-commit this file to the source control tool like Github.
- The service account key is a long-lived and powerful credentials.
- You don't know how to store it properly and quite easy to lose it.

## How we can make it more secure

To solve the issues mentioned above, we can make use of impersonating the service account. The idea is that instead of generating a new service account key and store it locally, you can generate a short lived token (normally for only 1 hour) and use it to pass the authentication/authorization.

As far as I know, there are two ways to do it:

- Before launching your service, do `gcloud auth login`
In this case, the service can access the credential from your local environment automatically. I tried this with cloud sql proxy and it seems to work fine.

- Generate the short-lived token on the fly and export it as environment variable

```bash
export GOOGLE_OAUTH_ACCESS_TOKEN=$(gcloud auth print-access-token --impersonate-service-account=<sa-name>.iam.gserviceaccount.com)
```

The above can easily visualized as below:

![generate a short lived token](/img/posts/impersonate-service-account/generate-short-lived-token.svg)

--- 
With that being said, you don't bother managing another service acount key anymore 😙!

## Reference

- [Managing service account impersonation](https://cloud.google.com/iam/docs/impersonating-service-accounts)
- [Creating short-lived service account credentials](https://cloud.google.com/iam/docs/creating-short-lived-service-account-credentials)
