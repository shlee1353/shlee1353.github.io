## GitHub Pages + hexo = blog

**1. GitHub Pages**

- create a repository
- clone the repository
- push it
- [Install](https://pages.github.com/)

**2. hexo**

[Installation](http://futurecreator.github.io/2016/06/14/get-started-with-hexo/)

```
$ npm install -g hexo-cli

$ hexo init myBlog
$ cd myBlog
$ npm install
$ npm install hexo-deployer-git --save

$ hexo server //localhost:4000
```

configuration

```
// _config.yaml

# URL
url: http://username.github.io/
.
.
# Deployment
deploy:
	type: git
	repo: https://github.com/username/username.github.io.git
	branch: master
```

upload

```
hexo generate
hexo deploy

or,

hexo g -d

```