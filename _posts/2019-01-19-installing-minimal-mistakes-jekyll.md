---
layout: single
title:  "Setting up jekyll with minimal-mistakes theme"
date:   2019-01-19 08:22:36 +1100
categories: jekyll theme
---
```bash
jekyll new test
```
```bash
owen@owen-linux:~/repos/test -> ls
404.html  about.md  _config.yml  Gemfile  Gemfile.lock  index.md  _posts
```
I have a couple of goals, which I think *should* be simple:

- install the minimal mistakes theme
- host on Github Pages
- be able to build it locally

Ok, now we have the enviroment setup, let's install the `minimal-mistakes` theme... oh, there's 3 ways to install it. 

I like the _idea_ of installing it remotely, let's do that.

So let's replace `Gemfile` to contain

```ruby
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins
```
Add `jekyll-include-cache` to `plugins` in `_config.yml`:

```yaml
plugins:
  - jekyll-feed
  - jekyll-include-cache
```

Ok, let's build the site and see if it works!

```bash
bundle exec jekyll serve
```

Uh oh!
```bash
Configuration file: /home/owen/repos/test/_config.yml
  Dependency Error: Yikes! It looks like you don't have jekyll-include-cache or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- jekyll-include-cache' If you run into trouble, you can find helpful resources at https://jekyllrb.com/help/!
jekyll 3.7.4 | Error:  jekyll-include-cache
```
So the required package is in my plugins, is the problem that it's not installed locally? Let's add it to the `Gemfile`

```
gem "jekyll-include-cache"
```
...run `bundle` again, and then `bundle exec jekyll serve`

This looks promising...
```bash
Configuration file: /home/owen/repos/test/_config.yml
            Source: /home/owen/repos/test
       Destination: /home/owen/repos/test/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
      Remote Theme: Using theme mmistakes/minimal-mistakes
...
```
It takes a while, because it's a remote theme, which for a local build might be slightly annoying. I'm sure it'll download those files locally and compare the git tags, right?

Oh no... another error:
```bash
Stopping at filesystem boundary (GIT_DISCOVERY_ACROSS_FILESYSTEM not set).
  Liquid Exception: No repo name found. Specify using PAGES_REPO_NWO environment variables, 'repository' in your configuration, or set up an 'origin' git remote pointing to your github.com repository. in /_layouts/default.html
             ERROR: YOUR SITE COULD NOT BE BUILT:
                    ------------------------------------
                    No repo name found. Specify using PAGES_REPO_NWO environment variables, 'repository' in your configuration, or set up an 'origin' git remote pointing to your github.com repository.
```
I need to set an environment variable? Hmmmm.... am I doing this right? I mean, why would I even want to build my site locally?

I'll keep shaving this yak for a bit longer... 

Let's add a `repository` to `_config.yml`

```yaml
repository: obrasier/test-site
```

Ok, lets' see if this works now. According to [this github issue ticket](https://github.com/jekyll/jekyll/issues/4705), I should be able to build locally by removing `gh-pages`, however, I _do_ want to host on Github Pages, so I want this step to work anyway.

Woo!

```bash
Configuration file: /home/owen/repos/test/_config.yml
            Source: /home/owen/repos/test
       Destination: /home/owen/repos/test/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
      Remote Theme: Using theme mmistakes/minimal-mistakes
       Jekyll Feed: Generating feed for posts
   GitHub Metadata: No GitHub API authentication could be found. Some fields may be missing or have incorrect data.
     Build Warning: Layout 'post' requested in _posts/2019-01-19-welcome-to-jekyll.markdown does not exist.
     Build Warning: Layout 'page' requested in about.md does not exist.
                    done in 263.116 seconds.
 Auto-regeneration: enabled for '/home/owen/repos/test'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

But my posts aren't showing... grrrrrrrr... seriously? Am I doing something dumb? Why can't stuff just work? Plus, the remote theme takes *ages* to build each time. I don't think this is going to work. How about I abandon the `remote_theme` idea and use a local one instead.

So update `_config.yml`
```yaml
theme: minimal-mistakes-jekyll
```
and add to the `Gemfile`
```
gem "minimal-mistakes-jekyll"
```

Ok! That builds super fast! I'm at the same point with the Remote themes. On the site... I just get a heading "Recent Posts" ... but wihtout any posts displaying.

Hmmmmmmm..... Let's continue! In the [Setup your site](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#setup-your-site) section, it says to replace the `index.md` with this contents, and to call it `index.html`, ok!

```
---
layout: home
author_profile: true
---
```

We also need to add the `pagination` settings because it's of layout `home` to `_config.yml`
```yaml
paginate: 5 # amount of posts to show
paginate_path: /page:num/
```
Is the post showing? No. Ok. 
![screenshot](/assets/images/pagination.png)


So that takes us to the end of the "Quick-start guide" ... shit isn't working. What do?

Ok, first things first, let's backup `_config.yml` and steal the one from [the repo](https://github.com/mmistakes/minimal-mistakes/blob/master/_config.yml). It has lots of settings, maybe we can get it working from a site that is working, and see if we can isolate the setting.

Make sure to uncomment the `theme: ` line

```yaml
theme                    : "minimal-mistakes-jekyll"
```

Boo ya! That works and displays all the things. Ok, settings not code is good. But which one was it? Do I even care? To be honest, not really.

What is a setting that looks like it could be a thing that loads a site sensibly? That _wasn't_ there by default.

Maybe it was a plugin thing? Swapping the plugins back actually made the posts load, and the sidebar on the left still didn't load. I am sure I'll learn more about these settings as I continue to use Jekyll. Anyway, try a verified configuration, even if you use `jekyll new` like I did. I think there's something missing in their guide, but I got it working now. Now to change the theme and add some personal configurations.

Changing to the `dark` theme, adding an avatar. I think my next post might be on editing the title colours in the theme, it's a bit... white.

But that's a post for another day. 




