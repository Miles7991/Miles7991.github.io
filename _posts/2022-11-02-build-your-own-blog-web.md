---
title: Build Your Own Blog Web by Github Pages
author: shiyu
date: 2022-11-02 17:13:00 -0600 
categories: [Github Pages]
tags: [Jekyll]
math: true
pin: true
---

This post is to show how I built this personal blog web based on Github Pages from scratch, the tutorial I followed, and some point you need to notice.

## Before You Start 

Read the [**Chirpy**](https://chirpy.cotes.page/posts/getting-started/) tutorial carefully if you like this website theme. At first I'm so in haste to check my personal page and didn't spend time on that tutorial. The result is I can run the website locally but after I push the project on the Repository it failed to generate the page on <https://Miles7991.github.io>. As a freshman in **Jekyll** and **HTML**, I waste a lot of time to find out what's the problem. Then I noticed that Chirpy already told me what should I do after clone this project. That's to run: 
```console
$ bash tools/init.sh
```
[**GitHub Page official Docs**](https://docs.github.com/en/pages/quickstart) is useful but not for the guy who want a quick start. Hope you guys can calm down and spend some time on the right tutorial. 



## Some Notices 

### Useful Website

1. [The **most** important tutorial by Chirpy](https://chirpy.cotes.page/posts/getting-started/)
2. [Quick introduction to Jekyll](https://stnava.github.io/lessons/2011/12/29/jekyll-introduction/)
3. [How to config your web by '_config.yml' file](https://jekyllrb.com/docs/configuration/)
4. [MarkDown Cheatsheet](https://aksakalli.github.io/jekyll-doc-theme/docs/cheatsheet/)


### Turn on the GitHub Action
After turning on the [GitHub Action](https://chirpy.cotes.page/posts/getting-started/#deploy-by-using-github-actions) you can build the project and test if the website is workable:

![GitHub Action](/postpic/20221102/githubaction.png)
_GitHub Action_  

## Customize Your Pages
I'm not a HTML guy but I do want to make some difference to the original web. In the following section I will introduce how to customize your pages.
### Change the Sidebar Picture
You can set the `img_cdn` on the **_config.yml** file, and this can be seen as the base address of your pic file but a url type which you can find on the github website by following steps:
1. Push the project contained the pic to the Repository.
2. Find the pic on Repository and  **Open the link in new tab** 
3. Copy the address of this image, put the front part of the link to `img_cdn` in my case is: `https://raw.githubusercontent.com/Miles7991/Miles7991.github.io/main/assets/img`{: .filepath}; and the end of the link is the name of each picture eg: `/sidebar/profile_photo.png`{: .filepath} or `/postpic/20221102/fig1.png`{: .filepath}

### Customize icons on the bottom of sidebar
There are some icon at the bottom of sidebar which you may want to customize. For me I add a phone icon which can show your phone number when someone click it. 

Find the file `_data/contact.yml` in the project, and add following code at the end of the file: 
```yml
- type: "phone"
  icon: "fas fa-phone"
  url: "tel:+1(XXX)XXX XXXX"
```
{: file='_data/contact.yml'}
For more icons, check the [**Font Awesome web**](https://fontawesome.com/)

### Change the position of the 'Mode-toggle Botton'
Chripy support two kind of mode of theme , namely 'dark' and 'light'. You can fix one of them in `_config.yml` file or letting the website switch between them. As default, the bottom located at the bottom of the sidebar. I move that on the top for personal taste.

Find the file `_includes/sidebar.html` in the project, move the section: 

```liquid
{% unless site.theme_mode %}
  <button class="mode-toggle btn" aria-label="Switch Mode">
    <i class="fas fa-adjust"></i>
  </button>

  {% if site.data.contact.size > 0 %}
    <span class="icon-border"></span>
  {% endif %}
{% endunless %}
```
under:
```html
<div id="sidebar" class="d-flex flex-column align-items-end">
```
And delete the `icon-border` section. Finally the button section will be like:
```html
{% unless site.theme_mode %}
  <button class="mode-toggle btn" aria-label="Switch Mode">
    <i class="fas fa-adjust" style="font-size:22px;color:gray"></i>
  </button>
{% endunless %}
```
{: file='_includes/sidebar.html'}