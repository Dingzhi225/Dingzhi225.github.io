# "DZ Studio" Personal Website
## Structure

The website contains the listed parts, which can be accessed from the navigation bar on the home page.  
```
├── _data # the specific items of shuoshuo part.
├── _posts # all posts published
├── about # the about page of the site
├── categories # the categories pages of all posts.
├── gallery # a gallery page contains many photo albums. all albums' covers are shown on the gallery page. 
│   └── photography # an album 
├── links # friend links and other useful links
├── live # the page of shuoshuo 
├── resume # personal resume about the host.
└── tags # the tags page of all posts
```

## Framework

This website was constructed based on the Hexo framework, which was built with Node.js and installed via npm. Learn more at [Hexo](https://hexo.io/zh-cn/). 

Hexo contains many themes (default "Landscape"). The website adopted the Hexo theme "Butterfly", whose source code can be cloned from <https://github.com/jerryc127/hexo-theme-butterfly>.


## Quick Start

1. Prerequisites

```
nodejs > 18
npm > 9
```

2. install Hexo and other dependencies

```bash
npm install 
```

3. Run server

```bash
npx hexo server
```
Then visit the site on <https://localhost:4000>

4. Deployment

The site was deployed from a branch "gh-pages"

Now the GitHub page can be visited on <https://dingzhi225.github.io/>

## Contact

Bug reports, imporvement suggestions:

<i class="fab fa-envelope"></i>: dingzhi0824@163.com