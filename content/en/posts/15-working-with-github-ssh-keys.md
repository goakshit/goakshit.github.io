---
title: "Github: Working With multiple SSH Keys"
date: 2022-01-24T22:46:41+01:00
draft: true
toc: false
images:
tags:
  - github
  - ssh
---

In this post, I will be discussing how to work with multiple github accounts and setup SSH keys for each account. 

Let's talk about steps on a broader level:
- Generate SSH keys for individual github account.
- Informing the github accounts about the keys.
- Testing the connection.

### Generate SSH keys for individual github account.
To generate a SSH key, we can run `ssh-keygen -t <ALGORITHM> -C <COMMENT> -f <FILE_NAME>` in the terminal.
```
ALGORITHM: dsa | ecdsa | ecdsa-sk | ed25519 | ed25519-sk | rsa
COMMENT: This is not required but is a good practice to add. Generally, **email** is used since it is unique to an github account.
FILE_NAME: For readability purpose. Eg: rsa_github_1
```

Example: `ssh-keygen -t rsa -C akshit@example.com -f github_1`

> Executing this command would expect us to enter a passphrase, which is optional. It's a good practice to set a passphrase.

Once it's done, you can find few files created under `~/.ssh` directory. There will be two files: `<FILE_NAME>` and `<FILE_NAME>.pub`. The former file is your private key and latter is the public key.

Now that we have a ssh key, we need to let our ssh-agent know about the same. We can run the command `ssh-add -K ~/.ssh/<FILE_NAME>`.

Example: `ssh-add -K ~/.ssh/github_1`

### Informing the github accounts about the keys.

