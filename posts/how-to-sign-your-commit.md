---
title: How to sign your commit.
description: Today I learned how I can check if the commits made to any of my account are from verified sources.
date: 2021-05-13
tags:
  - TIL
layout: layouts/post.njk
---

Today I learned how I can check the commits attributed to my account are not touched by malicious actors.

The idea is that you can [generate a GPG key](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-gpg-key) first, then add this key to your GitHub account, finally add an extra `-S` option whenever you make a commit. With the above steps,
you can see the `Verified` and `Unverified` status along with your commits.
![An image](/img/verified-commits.png)

## Steps

1. Install `gpg` and `pinentry-mac` binaries

```sh
brew install gpg pinentry-mac
```

2. Generate a new `gpg` key

```sh
gpg --full-gen-key
```

3. Follow the prompt messages to finish steps

Ref: https://docs.github.com/en/github/authenticating-to-github/generating-a-new-gpg-key

4. Get the `gpg` key id

```sh
gpg --list-secret-keys
```

5. Get the public key

```sh
gpg --armor --export YOUR_KEY_ID
```

6. Configure the public key to your git host

7. Configure git locally

```sh
git config --global gpg.program gpg
git config --global user.signingkey YOU_KEY_ID
```

8. Configure `gpg-agent`

```sh
echo "pinentry-program /usr/local/bin/pinentry-mac" >> ~/.gnupg/gpg-agent.conf
```

9. Restart `gpg-agent`

```sh
killall gpg-agent
```

10. Sign your commits whenever you commit

```sh
git commit -m "message" -S
```

(Optional) 11. Configure globally to sign all your commits

```sh
git config --global commit.gpgsign true
```

(Optional) 12. Back up your gpg keys

- Exporting

```sh
gpg --export --armor > gpg.keys.backup
```

- Importing

```sh
gpg --import gpg.keys.backup
```

That's it! Protect your repository with easy 10 steps 🤩!
