---
title: Deploy Hugo website on Github pages
subtitle: Step by step process to deploy Hugo website on Github pages
date: 2021-10-24
tags: ["Hugo", "Github"]
bigimg: [{src: "/img/triangle.jpg", desc: "Triangle"}, {src: "/img/sphere.jpg", desc: "Sphere"}, {src: "/img/hexagon.jpg", desc: "Hexagon"}]
---

In previous post, we walkthrough the process to create a Hugo website and to generate static files. 
The next process is to host the static files on the internet. 
Hugo documentation, which can be access at https://gohugo.io/hosting-and-deployment/, describes different ways to host and deploy Hugo website.


The conventional approach is to generate the static files from a Hugo website, and host these files with your choice of hosting providers. 
The other approach is to host the Hugo website as a source control repository, and create a pipeline to generate static files and publish them with a hosting provider. 
The pipeline can be triggered on every push to the repository. 

In this post, we use the Github as both for source control repository, pipeline and hosting providers. 
Github pipeline or Github Action is introduced in 2018, and it is a late comer compared to other source control provider such as bitbucket and Gitlab  which already introduce pipeline in their offering. 
Github pages is static web hosting service offered by Github for free since 2008. 

Github offer a Github page per account. In addition to that, every project can have their own Github pages. 
The requirements is slightly different between two. For the github account the page is accessed via URL ***https://[account name].github.io.***
For the project or repository Github page, it is accessed via URL ***https://[account name].github.io/[repository name].***

For an account Github page, the static files must be hosted and deployed at repository with name ***[Github account name].github.io***
For a repository Github page, the static files must be hosted and deployed at the same repository.

You can configure where the static files should reside from the repository settings. 
The configuration allow you to select the branch and the directory for the static files. The configuration limit the directory to either ***root*** or ***docs*** directory (see below)

![configuration for Github pages](/img/ghpage.png)

In this post, we create a Gihub page for the Github account. So here is the step required.

1. ***Create a Hugo website locally***    
   To see this step go to previous post
2. ***Modify the config.toml***      
   Add one line above title in the ***config.toml***
    ```
   baseurl = "https://[account name].github.io"
   title = "[Your name]"
   theme = "beautifulhugo"
   ```
3.  ***Add Github action to the Hugo website***    
    create a new file and put them in the ***.github/workflow/gh-pages.yml***
	```
	name: github pages

    on:
      push:
        branches:
          - main
    jobs:
      deploy:
        runs-on: ubuntu-20.04
        steps:
          - uses: actions/checkout@v2.3.5 
          - uses: peaceiris/actions-hugo@v2.5.0 
          - run: hugo --minify 
          - uses: peaceiris/actions-gh-pages@v3.8.0 
            if: ${{ github.ref == 'refs/heads/main' }}
            with:
              personal_token: ${{ secrets.PERSONAL_TOKEN }} 
              external_repository: [accout name]/[account name].github.io
              publish_dir: ./public      
              publish_branch: main
              destination_dir: ./
              force_orphan: true
	```
	The file name is arbitrary.
	You just need to change your Github account name in the script above. 
    For the ***secrets.PERSONAL_TOKEN***, that is a variable name you need to setup in the next step.
4. ***Create Personal Token on Github pages***
   In order to publish the static files to external repository as indicated in the script in previous step, we need access token to commit to the [account name].github.io
   It is a two step process.      
   First, we need to create a personal access token. 
   * go to Github account ***settings*** (click profile account on the top right and select Settings)   
   * select ***Developer settings*** tab
   * select ***Personal access token*** tab
   * click ***generate  new token*** button
   * Input name, set No expiration, and select workflow scopes
   * click ***generate token*** button
   * copy the key generated     
   (see picture below)
   ![Personal access token](/img/personal-access-token.png)
   
	Second, we need to set variable in the Hugo webiste repository.
   * go to Hugo website repository ***settings***
   * select ***Secret*** tab
   * click ***new repository secret*** button
   * input Name with ***PERSONAL_TOKEN***
   * input Value with the generated key
   * click ***Add secret*** button
 
5.  ***Create  [accout name].github.io repository***
    Next, we need to create [account name]github.io  repository to host the static files. 
	Got to your Github website, and create a new repository with above name. 
	You can add a license just to make sure it create the main branch my default, and add the first commit.
    Note the script in step 3 will overwrite whatever commit in the branch. 	
6. ***Configure the Github page***
   By default the Github page URL for the newly created repo in step 5 will be [account name].github.io.
   If you create any other repo the Github page URl is [account name].github.io/[repository name].
   The script in step 3 will deploy the static files at the root directory on the main branch. So nothing to configure for this step. 
   If you change the ***publish_branch*** and ***destination_dir*** in step 3, you will need to change the destination repo Github page setting configuration.
   To access this
   * go to the destination repository in the Github website
   * select ***settings***
   * select ***Pages*** tab
   * select ***the branch*** and ***directory*** (root or docs) for the Github page
7.  ***Create a repo for the Hugo website***
    The last step is to push the changes in the local repo to remote repo. 
	Since we assume there is no remote repo yet for the Hugo website, here is the step
	* Init as local repo
	* make a local commit
	* create remote repo
	* push remote repo.
	

Step to create a new blog post:
* Add an md file under ***content/post*** of the local Hugo website.    
   You can run ***hugo server*** command, and browse your blog locally while you are creating or modifying your md file.
* Push your change to remote.
   ```
   git add .
   git commit -m "[your post]"
   git push
   ```


   













