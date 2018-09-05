---
layout: single
title:  "[WIP] How to Set Up a Simple Product Website with Jekyll, S3 and Cloudflare"
date:   2018-09-04 16:15:50 +0200
tags: 
  - devops
  - webdev
toc: true
toc_label: "Table of Contents"
toc_icon: "book"  # corresponding Font Awesome icon name (without fa prefix)
---
This is the first part of a series of articles about how to set up a website promoting a simple product. It is written as a step-by-step walk-through that should help anyone who wants to do something similar. 
More articles will appear as the project moves forward.

The goal is to have a very simple website which promotes one product in multiple languages and will be expandable with a shopping and newsletter functionality. You can see the result at [Feynman Company](https://www.feynman-company.com/en/).

## The Website
I decided to go with [Jekyll](https://jekyllrb.com/) as a website framework because it generates static HTML, which is easy to host, fast to serve, and completely sufficient for the planned functionality.

### Setting up Jekyll
Setting up Jekyll is quite easy, I just followed the [Quickstart Guide](https://jekyllrb.com/docs/#instructions) on the Jekyll website.

1. Installing jekyll and bundler  
    ```bash
    > gem install jekyll bundler
    ```
2. Setting up the website in a folder  
    ```bash
    > jekyll new my-website
    ```

Now you're set up with a basic website in the default theme. To try it out just run ```cd my-website; jekyll serve```.

For this project I decided to use and modify the default theme ```minima```. So I went to the [github page](https://github.com/jekyll/minima) of the minima theme and copied the whole content of the repository into the ```my-website``` directory. 

Then I did some clean up to start with a clean slate:
 - Some of the files and like ```.travis.yml```, ```History.markdown``` or ```script/*``` are not needed for the theme so they can just be deleted.
 - I didn't plan on using the concept of posts at all, so I deleted the ```_posts/``` directory and and other related files like ```_layouts/post.html```.
 - Rename the ```assets``` directory to ```_assets``` so it isn't automatically copied into the deployment folder when running ```jekyll serve``` or ```jekyll build```

### Installing some Jekyll Plugins

**Note:** Because I'm using Ruby just for Jekyll right now I'm not using RVM or install gems into a custom directory right now, but you should definitely look into that if you're managing multiple Ruby projects on one machine. 
{: .notice--info}

Because the site will have german text on it, I installed [jekyll-umlauts](https://github.com/ArneGockeln/jekyll-umlauts) by copying the ```jekyll-umlauts.rb``` into the ```_plugins/``` folder (you might have to create the folder first). It automatically replaces german Umlauts with their correct HTML entities.

The next plugin is [jekyll-sitemap](https://github.com/jekyll/jekyll-sitemap) which generates a ```sitemap.xml``` for all your pages and posts, which then gets parsed by search engines.

A bit more work is needed to install [jekyll-assets](https://github.com/envygeeks/jekyll-assets), it's not documented too well, but I ended up adding the following items to the ```Gemfile``` (don't forget to run ```bundle update``` after adding new items to the ```Gemfile```):

```
group :jekyll_plugins do
  gem "jekyll-assets"
end

gem "mini_magick", "~> 4.8.0"
gem "autoprefixer-rails"
gem "uglifier"
gem "closure-compiler"
```

The config defaults seem to work pretty well, I only needed to configure the auto-prefixer by adding this to ```_config.yml```:

```yml
assets:
  plugins:
    css:
      autoprefixer:
        browsers: ["> 1%","last 2 versions"]
```

Jekyll Assets will resize images when building the website and also generate srcsets for ```<img>``` and ```<picture>``` tags, which work for different sizes of images in the website as well as for "hi-dpi" images.

### Adapting the Design
I decided to keep the basic color scheme of the minima theme in the beginning and modify the layout by setting up a basic, responsive grid using [Bourbon Neat](https://neat.bourbon.io).
To set up Neat, just:
1. Install the ```neat``` gem, which I did by adding it to the ```Gemfile```:  
    ```
    gem "neat"
    ```
2. Install it:
    ```bash
    > bundle update
    ```
3. Go to your css directory and install the scss files:
    ```bash
    > cd _assets/css
    > neat install
    ```
4. Import it in your main scss file:
    ```scss
    @import "neat/neat";
    ```

Then I went on to configure a responsive grid:

```scss
$article-layout-grid: (
  columns: 1,
  gutter: 2rem,
  media: 1200px,
);

$article-layout-grid--tablet-up: (
  columns: 5,
  gutter: 2rem,
  media: "(min-width: 768px)",
);

.article-layout {
  @include grid-container;
}

.article-layout__max_width {
  @include grid-container;
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
}

.article-layout__hero {
  @include grid-column(1, $article-layout-grid);
  margin-bottom: 2rem;
  margin-left: 0;
  margin-right: 0;
}

.article-layout__main_text {
  @include grid-column(1, $article-layout-grid);
  margin-bottom: 2rem;
  font-size: large;
  font-weight: lighter;
  line-height: 2;
  text-align: justify;
  text-indent: 1em;

  @include grid-media($article-layout-grid--tablet-up) {
    @include grid-column(2);
  }
}

.article-layout__main_img {
  @include grid-column(1, $article-layout-grid);
  margin-bottom: 2rem;

  @include grid-media($article-layout-grid--tablet-up) {
    @include grid-column(3);
  }
}

.article-layout__footer {
  @include grid-column(1, $article-layout-grid);
  margin-bottom: 2rem;

  @include grid-media($article-layout-grid--tablet-up) {
    @include grid-column(2);
  }
}
```

Using jekyll-assets, including static files like CSS files is very easy, just add the reference into ```_includes/head.html```.  In this case the file is called ```main.scss``` but you have to include it by its compiled name ```main.css```:
```markdown 
{% raw %}{% asset main.css %}{% endraw %}
```

## Hosting
To minimise time spent on administration I chose to host the website on [AWS S3](https://aws.amazon.com/s3/) with [Cloudflare](https://www.cloudflare.com/) as a frontend which will handle SSL and caching.

More coming soon ...

## Configuration and Analytics

### robots.txt

Coming soon ...

### Favicon files

Coming soon ...

### Google Search Console

Coming soon ...

### Google Analytics

Coming soon ...
