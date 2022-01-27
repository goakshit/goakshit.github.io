---
title: "Github: Working With multiple SSH Keys"
date: 2022-01-24T22:46:41+01:00
draft: false
toc: false
images:
tags:
  - github
  - ssh
  - git
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

Once it's done, we can find few files created under `~/.ssh` directory. We can go to this directory using `cd ~/.ssh` . There will be two files: `<FILE_NAME>` and `<FILE_NAME>.pub`. The former file is your private key and latter is the public key.

Now that we have a ssh key, we need to let our ssh-agent know about the same. We can run the command `ssh-add -K ~/.ssh/<FILE_NAME>`. It may ask us to enter passphrase that we entered in the previous step.

Example: `ssh-add -K ~/.ssh/github_1`

Now, lets update the ssh config file (`~/.ssh/config`) and add following content to the file.
```
Host <HOST_NAME>                           // This can be anything. Eg: github_acc_1
  HostName github.com
  User git
  IdentityFile ~/.ssh/<FILE_NAME>         // This is the ssh-key that we created
```

### Informing the github accounts about the keys.

This is probably the easiest step. We need to login to our github account. Follow the steps described below:
- Go to `Settings` by clicking on icon dropdown on the header.
- Select the `SSH and GPG keys`.
- Click on the `New SSH key` button.
- Give it any title.
- Copy the contents of `~/.ssh/<FILE_NAME>.pub` to Key Section. Then, click on `Add SSH key` button and we are done.

### Testing the connection

Easiest way to test is to clone the repository. We can run the following command:
`git clone <HOST_NAME>:<ORGANISATION_NAME/USER_NAME>/<REPOSITORY_NAME>.git`.

Here, `HOST_NAME` is the one that we gave in ssh config file. 
Eg: `git clone github_acc_1:goakshit/sauron.git`

We have successfully setup ssh with github. To work with multiple github accounts, repeat 1 and 2 again. But in ssh config file, add another HOST below the existing entry.

To clone a repository under different account, we just need to use appropriate HOST name. Thats all.

>Thanks for reading all the way through. You can refer [github docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/about-ssh) for in-depth knowledge on the topic.
