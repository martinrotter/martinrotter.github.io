---
layout: post
title: Pushing to arbitrary Git repositories via Travis CI job
author: Martin Rotter
date: 2016-08-26 09:00:00 +0200
category: it-programming
language: en
---

Sometimes you may need to clone some custom Git repository and use it when your application gets compiled on [Travis CI](https://travis-ci.org/). This simple tutorial will show you how to do it in a secure way.
<!--more-->
Let's suppose that your Git repositories, you want to work with, are stored on GitHub. You need to setup new personal access token on GitHub:

1. Go to your GitHub user settings and select section "Personal access tokens".
2. Generate new token, let's represent token's value for purpose of this tutorial as `<TOKEN>`.

Now, you need to let Travis CI know about your access token:

1. Go to your project page on Travis CI and open its settings.
2. Add new environment variable, name it `GH_TOKEN` and set `<TOKEN>` as its value.
3. Save your settings.

Now, you need to write the actual script which makes use of the token and does whatever you want to do with your custom Git repositories. Travis CI works via `travis.yml` configuration file, which is placed usually in the root folder of your project. This configuration file offers several sections where you can execute your scripts, depending in which phase of project building you want to execute your custom actions.

Let's suppose you want to perform some steps when your Travis CI build is successfull. In that case, add this to your `travis.yml` file:

```bash
after_success:
 - # Set your identification.
 - git config --global user.email "<YOUR_GITHUB_EMAIL>"
 - git config --global user.name "<YOUR_GITHUB_USERNAME>"
 - 
 - # Clone some repository you want into "repo" subfolder.
 - git clone https://<YOUR_GITHUB_USERNAME>:${GH_TOKEN}@github.com/<REPO>.git ./repo
 - 
 - # Now, perform your custom actions. Custom repository is cloned int "repo" subfolder of PWD.
 - 
 - # After your actions are done, you might want to commit and push your changes.
 - cd repo
 - git commit -m "Actions done."
 - git push
```

The key thing is that you use your personal access token via `GH_TOKEN` environment variable. It is visible in `git clone` step.