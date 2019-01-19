---
layout: single
title:  "Setting up jekyll with minimal-mistakes theme"
date:   2019-01-19 08:22:36 +1100
categories: jekyll theme
---
Ok so we want to make a new jekyll blog. Cool! First things first, have `jekyll` installed in a local scope and add `gems` to your `PATH` in `~/.bashrc` etc etc. 

I am assuming you have an environment set up, let's go!

```bash
jekyll new obrasier.github.io
```
```bash
owen@owen-linux:~/repos/obrasier.github.io -> ls
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

```ruby
gem "jekyll-include-cache"
```
...run `bundle` again, and then `bundle exec jekyll serve`

This looks promising...
```bash
Configuration file: /home/owen/repos/obrasier.github.io/_config.yml
            Source: /home/owen/repos/obrasier.github.io
       Destination: /home/owen/repos/obrasier.github.io/_site
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
repository: obrasier/obrasier.github.io
```

Ok, lets' see if this works now. According to [this github issue ticket](https://github.com/jekyll/jekyll/issues/4705), I should be able to build locally by removing `gh-pages`, however, I _do_ want to host on Github Pages, so I want this step to work anyway.

Woo!

```bash
Configuration file: /home/owen/repos/obrasier.github.io/_config.yml
            Source: /home/owen/repos/obrasier.github.io
       Destination: /home/owen/repos/obrasier.github.io/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
      Remote Theme: Using theme mmistakes/minimal-mistakes
       Jekyll Feed: Generating feed for posts
   GitHub Metadata: No GitHub API authentication could be found. Some fields may be missing or have incorrect data.
     Build Warning: Layout 'post' requested in _posts/2019-01-19-welcome-to-jekyll.markdown does not exist.
     Build Warning: Layout 'page' requested in about.md does not exist.
                    done in 263.116 seconds.
 Auto-regeneration: enabled for '/home/owen/repos/obrasier.github.io'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

But my posts aren't showing... grrrrrrrr... seriously? Am I doing something dumb? Why can't stuff just work? Plus, the remote theme takes *ages* to build each time. I don't think this is going to work. How about I abandon the `remote_theme` idea and use a local one instead.

So update `_config.yml`
```yaml
theme: minimal-mistakes-jekyll
```
and add to the `Gemfile`
```ruby
gem "minimal-mistakes-jekyll"
```

Ok! That builds super fast! I'm at the same point with the Remote themes. On the site... I just get a heading "Recent Posts" ... but without any posts displaying.

It looks like this:

![screenshot](/assets/images/pagination.png)

Hmmmmmmm..... Let's continue! In the [Setup your site](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#setup-your-site) section, it says to replace the `index.md` with this contents, and to call it `index.html`, ok!

```yaml
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
Is the post showing?

![screenshot](/assets/images/pagination.png)

... I guess not. Let's continue.

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

Ok, let's push and see if it displays on github:

![*... blank white page ...*](/assets/images/white.png)

Nooooooooooooooooooooo!

Ok, there's some configuration that I haven't added. According [to this](https://stackoverflow.com/questions/49071139/jekyll-github-works-on-local-but-empty-page-on-https-username-github-io) you need to use the `remote_theme` feature. LOL. Back to square one.

So make the `_config.yml` like this.
```yaml
# theme                    : "minimal-mistakes-jekyll"
remote_theme             : "mmistakes/minimal-mistakes"
```

...and it works! Yay!

It takes exactly... a long time for it to pull down the remote theme on my local computer, but whatevs, it still builds. 🤷‍♂️

```
done in 303.062 seconds.
```
🤦‍♂️

Looking at the Quick-start guide it says "Remote themes are similar to Gem-based themes, but do not require Gemfile changes or whitelisting making them ideal for sites hosted with GitHub Pages."

... and it doesn't say...

"in order to host on Github Pages you **must** use a `remote_theme`." Although to be fair it's not up to the theme to tell everyone how Github works. Just for a n00b like me, it would have been helpful. That's all.

Cool! So that works, now I can write stuff that noone can read, yay! I'll look into customising the theme soon, which I imagine involves making local versions of the theme files, and yay CSS, my favourite thing! 

Plus, that build time makes me want to punch people. So it might be that I'll have to fork the theme repo and host it all locally. I guess that works, it won't do the versioning thingy-thing, but do I really care?

I care about not wanting to punch stuff, so I might just do that. Plus, I'll want to edit some stuff anyway, so this remote stuff is a kind of nice idea, but also actually just annoying. 

The internet is not that good just yet that we can treat everything is on a LAN, not in Australia anyway.