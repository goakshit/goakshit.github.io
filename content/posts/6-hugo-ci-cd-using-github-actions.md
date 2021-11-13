---
title: "Setup CI-CD For Hugo Website Using Github Actions"
slug: "hugo ci-cd using github actions"
tags: ["hugo", "ci-cd", "github-actions"]
date: 2021-08-02T14:55:52+05:30
draft: false
---
![](/img/6_1_ci_cd_banner.jpeg)
## What is CI/CD?
> Continuous integration (CI) and continuous delivery (CD) embody a culture, set of operating principles, and collection of practices that enable application development teams to deliver code changes more frequently and reliably.

In short, CI/CD is a way to automate the process of building, testing and deploying code. The cycle of building till deploying is also referred to as pipelining.


## Let's jump right in.

**Prerequisite**: A GitHub account with hugo website and understanding about github pages

### Generate Personal Access Token(PAT)

To generate a PAT, open [Github Personal Access Token](https://github.com/settings/tokens/new). Ensure that you are logged in to github.
Give token a self-explanatory name, select `workflow` scope, expiration `No Expiration` and then click `Generate Token`.

### Create Hugo Repo Action Secret

Once token is generated, copy the token and go-to your hugo repository. Under repo settings, under `Secrets` section, click on `New repository Secret` and give it an appropriate name and paste the secret there as shown below.

![](/img/6_2_repo_secrets.png)

### Setting up Github Actions

Click on `Actions` tab in your hugo repository and click on `New` button.
Copy the contents of the below yaml file.
```yaml
name: ci-cd

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the master branch
  push:
    branches: [ master ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2
        with:
            submodules: true
    
      - name: Setup hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "latest"
          extended: true
      
      - name: Build
        run: hugo --minify
        
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.WORKFLOW_TOKEN }}
          external_repository: goakshit/goakshit.github.io # You hugo repository
          publish_dir: ./public # Change it to directory where you build your hugo site. By default, it's public
          #   keep_files: true
          publish_branch: gh-pages # This is the branch where your site will be published
        #   cname: example.com
```

After copying the contents of the yaml file, click on `Start commit` button on right. It should start the build process.

Every commit to master branch will trigger the build. 
> Voila! CI/CD is now setup.
