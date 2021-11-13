+++
date = "2021-02-11"
draft = false
title = "Deploy hugo blog on github pages"
slug = "hugo github pages"
tags = ["hugo", "github-pages", "deploy"]
+++
## What is Hugo?
> Hugo is a fast and modern static site generator written in Go, and designed to make website creation fun again.

In short, Hugo is a static website generation tool which transforms [markdown](https://en.wikipedia.org/wiki/Markdown) into beautiful web pages. One can get started with hundreds of available themes or even create a new one.

Let's jump right in. 

- First of all, we would need to install Hugo. In macOS, it can be installed through homebrew by running **` brew install hugo`**
- Once it is installed, we can create a blog by running **` hugo new site <BLOG_NAME>`**
- Now that the basic blog boilerplate is there, we can serve the blog locally going into blog directory created just now and run **` hugo server`**. You can open up **localhost:1313** in your browser and you have your blog up and running.

Now coming to deploying the blog to Github pages. 
- We need to create a repository with the name (**<YOUR_GITHUB_USERNAME>.github.io**). While creating a repository, don't add a readme file to it yet and create a repository.
- To deploy the blog, we need to build it. Go to blog directory and run **` hugo`**. This will create a `public` directory or the name that we have configured in `config.toml` file.
- Now we need to commit files to the repository. 
```
git add .
git remote add origin
git remote add origin https://github.com/<GITHUB_USERNAME>/<GITHUB_USERNAME>.github.io.git
git commit -m "Initial commit"
git push origin master
``` 
- Now that our files are there in the repository's master branch, we need to push the `public` directory to gh-pages branch for the website to be available. We will create a subtree from the `public` directory and push the `public` directory to `gh-pages` branch.
```
git add public && git commit -m "Initial public subtree commit"
git subtree push --prefix public origin gh-pages
```
- The last step would be to go-to repository settings and under `Github pages` section, update the Source to `gh-pages` branch and save it. Our blog should then be available at **https://<YOUR_GITHUB_USERNAME>.github.io**
- We created a subtree with only `public` directory. So whenever we update the website in future, we will commit the changes in the master branch but for changes to be reflected in the website, we need to update the subtree and push the changes to the gh-pages branch. So, after we build the updated blog using **`hugo`** command, we need to run the following command that pushes the updated     `public` directory to `gh-pages` branch.
```
git push origin `git subtree split --prefix public master`:gh-pages --force
```

You can add themes, about page and so on. All that information is available at [hugodocs](https://gohugo.io/documentation/). Hope it helps someone who wants to deploy a blog in no time. There is an easier way to deploy that involves storing Hugo code in one repository and build files in another. But I didn't want to create two repo for the same. 

You can reach me out on [Twitter](https://twitter.com/dharakshit) or write to [me](mailto:akshitdhar@live.com)