# blog [![Netlify Status](https://api.netlify.com/api/v1/badges/d24bd798-d1b6-44b5-a899-f72db5285857/deploy-status)](https://app.netlify.com/sites/relaxed-volhard-6ea0ee/deploys)
This repo is the source code for [my personal website ](https://minnmyatsoe.com)

## Development
The website is built with [Hugo](https://gohugo.io). It's a popular static generator. Hugo is written in Go.

The main theme is [hello-friend-ng](https://github.com/rhazdon/hugo-theme-hello-friend-ng), with some customization. The changes are in `./assets/scss/` and `./layouts/` directories.

To run in local environment, run the server and navigate to `http://localhost:1313`
```
> hugo server                                                                                       

                   | EN  | FR
-------------------+-----+-----
  Pages            | 169 | 10
  Paginator pages  |   1 |  0
  Non-page files   |   0 |  0
  Static files     |  22 | 22
  Processed images |   0 |  0
  Aliases          |  73 |  3
  Sitemaps         |   2 |  1
  Cleaned          |   0 |  0

Built in 114 ms
Watching for changes in /Users/minn/Code/site/blog2/{archetypes,assets,content,data,layouts,static,themes}
Watching for config changes in /Users/minn/Code/site/blog2/config.toml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at //localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

I code and write in [Visual Studio Code](https://code.visualstudio.com/) on macOS. 

## Deployment
The website runs on [Netlify](https://www.netlify.com), which cotinuously deploys from this [Github](https://github.com) repo. It also enables HTTPS and acquires site certficate from Let's Encrypt.
