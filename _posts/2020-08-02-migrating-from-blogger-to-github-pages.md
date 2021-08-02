---
layout: post
title: Migrating from Google Blogger to GitHub pages
---

The post covers how to migrate from Google Blogger to GitHub pages using the wonderfully simple [Beautiful Jekyll project](https://github.com/daattali/beautiful-jekyll).

Pre-requisites: 
* Some knowledge of Git.
* Ruby and bundler must be installed.

Thank you to [Kris Rice](https://krisrice.io/2017-10-06-migrating-my-blog-from-blogger-to-github-pages-with-jekyll/) and [Dylan Beattie](https://dylanbeattie.net/2019/08/14/migrating-from-blogger-to-github-pages.html). This blog was cobbled together mostly from a combination of their blog posts on the same subject.

## Create your GitHub Pages repository

Beautifl Jekyll makes it super simple to create a GitHub Pages blog.

* Fork [the project](https://github.com/daattali/beautiful-jekyll).
* Rename the repository to username.github.io
* Edit `_config.yml` file.

For more information read the Beautiful Jekyll [documentation](https://github.com/daattali/beautiful-jekyll#main-parameters).

## Import your old blog posts

First, export all the posts from Blogger. Go to Settings -> Back up content. It will download an XML file.

Clone your GitHub Pages repository and place the XML file at the root.

Blog posts are imported using [Jekyll Import](https://github.com/jekyll/jekyll-import). Run `gem install jekyll-import` to install it.

To import your old posts change the XML file path and execute:
```ruby
ruby -r rubygems -e 'require "jekyll-import";
    JekyllImport::Importers::Blogger.run({
      "source"                => "path-to-export.xml",
      "no-blogger-info"       => false, # not to leave blogger-URL info (id and old URL) in the front matter
      "replace-internal-link" => false, # replace internal links using the post_url liquid tag.
    })'
```

This will create a bunch of new files in the posts directory. At the top of each file are some properties in markdown. E.g. Title, tags, author. The rest of the post will be created as HTML rather than markdown. You may want to check the styling looks okay and images are being shown by running the server locally using `bundle exec jekyll serve`.

## Add redirects

The links to all your blog posts are going to change so lets make sure the old URL links to the new URL. This can be done using [Jekyll redirect from](https://github.com/jekyll/jekyll-redirect-from).

Install it by adding `gem 'jekyll-redirect-from'` to the Gemfile then executing `bundle`. Configure by adding the `redirect_from` property to each imported post. IThis can be created by removing the domain from the `blogger_orig_url` property. For example:

```
blogger_orig_url: http://www.philjhale.com/2016/04/book-review-dont-make-me-think.html
redirect_from: /2016/04/book-review-dont-make-me-think.html
```

There are many ways to do this. I used a regex find and replace in files using VSCode. 

* Find regex: `blogger_orig_url: http://www.philjhale.com(.*)`
* Replace regex: `$0\nredirect_from: $1`


## Configure a custom domain

The next step is to change www.philjhale.com to point to GitHub pages rather than Blogger. Remove the custom from from Blogger. Blogger -> Settings -> Custom domain -> Delete.

Add your domain to GitHub Pages. Repository -> Settings -> Pages -> Custom Domain -> www.philjhale.com -> Save.

Go to your DNS provider and change the www CNAME record to point www.philjhale.com at philjhale.github.io. It may take up to 24 hours for the DNS changes to take affect. For more information see [the GitHub documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain).

And you're done.