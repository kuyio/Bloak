# Bloak

Bloak is a Ruby on Rails engine to provide the functionality of a micro-blog with articles written in Markdown. It includes an attractive admin UI that makes managing and writing new blog articles a breeze. Bloak is lightning-fast, and optimized for SEO, accessibility and supports open-graph meta-tags for rich social media sharing. Easy customization and styling makes Bloak a joy to integrate into your existing web-application and match the host application's look and feel.

## What Does it Look Like?

The Bloak engine was developed in-house to power [our own Blog](https://kuy.io/blog), showcasing a custom style to integrate nicely with our main website.

## Features

- [x] Responsive and mobile friendly
- [x] Google Lighthouse score of 90+ on all categories
- [x] Write Blog posts in Markdown format (extended Github-flavoured markdown)
- [x] SimpleDME markdown editor integration
- [x] Custom Markdown tags for info, warning, quote boxes, table-of-contents and more
- [x] Syntax highlighting for fenced code blocks provided by Rouge
- [x] Custom Markdown renderer supports ERB and HTML tags in Markdown
- [x] Cover images for posts with automatic resizing of preview images
- [x] Post categories
- [x] Filtering for categories
- [x] Full-text search for posts
- [x] Open-Graph meta tags for sharing posts with Twitter, Facebook, Linked-In
- [x] SEO meta tags for blog posts
- [x] Author gravatar images
- [x] Image uploads
- [x] Reading time estimation for articles
- [x] (Optionally) Featured articles that are always displayed on top
- [x] Extensible and customizable view templates and styles
- [x] Admin Interface with authentication
- [x] Uses Bootstrap 5 front-end framework
- [x] Uses Fontawesome 5 icons

### Google LightHouse Scorecard

Google Lighthouse is a free tool that provides a report analyzing page experience and performance. Lighthouse has an increased emphasis on-page user experience, including adding a new set of Core Web Vital signals. The signals break down how a user experiences the page. One of the core design goals for Bloak was lightning-fast, great on-page user experience, so we pay special attention to the LightHouse scores for each release.

![scorecard](docs/bloak_lighthouse_score.png)

## Installation

Usually, specifying the engine inside your application's `Gemfile` would be done by adding it as a normal, everyday gem. However, because we have not released the `Bloak` engine on the official rubygems.org repository, we will need to specify the `git` option:

```ruby
# Blog
gem 'bloak', git: "https://github.com/kuyio/bloak.git"
```

Then run `bundle` to install the gem.

As described earlier, by placing the gem in the `Gemfile` it will be loaded when Rails is loaded. It will first require `lib/bloak.rb` from the engine, then `lib/bloak/engine.rb`, which is the file that defines the major pieces of functionality for the engine.

To make the engine's functionality accessible from within an application, it needs to be mounted in that application's `config/routes.rb` file:

```ruby
mount Bloak::Engine, at: "/blog"
```

The engine contains migrations for the `bloak_articles` and `bloak_images` tables which need to be created in the application's database so that the engine's models can query them correctly. To copy these migrations into the application run the following command from the application's root:

```sh
$ bin/rails bloak:install:migrations
```

Additionally, Bloak requires `ActiveStorage` to be installed in your Rails application to store images for your posts. If you haven't done so yet, now is a good time to run `bin/rails active_storage:install`. Please note, that Bloak uses the `image_processing` gem to create thumbnails and image variants, which in turn requires the `vips` linrary to be installed on your system.

Then, run all migrations within the context of the application with `bin/rails db:migrate`.

## Configuration

You can configure the Bloak engine through an initializer file at `main_app/config/initializers/bloak.rb`

```ruby
Bloak.configure do |c|
# The name of the site, used in the Navigation Bar, and footer unless a copyright is set
  c.site_name = "My Awesome Blog"

  # The copyright notice in the footer
  c.copyright = "© 2023 My Awesome Company - all rights reserved"

  # The username for the admin user
  c.admin_user = ENV.fetch('BLOAK_ADMIN_USER')

  # The password for the admin user
  c.admin_password = ENV.fetch('BLOAK_ADMIN_PASSWORD')

  # The number of blog posts to show before pagination
  c.num_items = 10

  # The maximum number of featured posts to display
  c.num_featured_posts = 3

  # The maximum depth to render for the TOC of a post
  c.max_toc_depth = 3
end
```

**Note:** You must assign a value to `admin_user` and `admin_password`, or Bloak will not serve the admin routes and raise an exception instead.

Also make sure to set your `default_url_options`, so absolute URLs to your assets can be correctly generated, for example in `config/application.rb`:

```ruby
# Default Host for URL Helpers
routes.default_url_options[:host] = 'blog.example.com'
routes.default_url_options[:protocol] = 'https'
```

## The Admin Interface

You can access the admin interface under the `/admin` sub-path of your engine mount, for example, if you mounted the engine at `/blog` the admin UI is available at `/blog/admin`. The Admin UI is secured by HTTP Basic Auth and both `admin_user` and `admin_password` muste be set in the `Bloak` configuration (see above).

Within the admin UI, you can upload images for embedding within Blog posts, as well as write and manage Blog posts.

## Writing Posts

Post content can be written in GitHub-flavoured markdown syntax with a few custom Markdown tag extensions. Additionally we support `HTML` tags and `ERB` tags (see below).

### Custom Markdown Tags

The `Bloak` Engine includes a custom Markdown render, that introduces a number of additional tags beyond GitHub-flavoured Markdown.

- `!! text` renders a danger alert box with icon and the text given in the paragraph
- `!w text` renders a warning alert box with icon and the text given in the paragraph
- `!i text` renders an info box with icon and the text given in the paragraph
- `!q text` renders a quote box with icon and the text given in the paragraph
- `!media[name]` inserts an Image (make sure to upload and name it) identified by its unique name
- `!toc` or `!toc[label]` inserts a level-1 table of contents at this position, with an optional `label`

### ERB Content

The custom Markdown engine also supports `ERB` tags outside of fenced code blocks, in the Markdown content of your article.
You can pass local variables (`assigns`) into the ERB template engine by passing a Hash to the `Bloak::Post.render()` method.

## Customizations and Styles

You can create or copy the following files from the Bloak engine to customize the rendering of Header, and Footer, as well as to include custom styles and JavaScripts:

```
views/
  bloak/
    application/
      _stylesheet.html.erb
      _javascript.html.erb
      _meta_tags.html.erb
      _header.html.erb
      _footer.html.erb
```

You can create or copy the following files from the Bloak engine inside the host application to customize the rendering of Post index, Post display and display of search results:

```
views/
  bloak/
    posts/
      index.html.erb
      show.html.erb
      search.html.erb
      _topics.html.erb
      _post_list.html.erb
```

You can customize the brand logo rendered in the navbar by adding your own file to `app/assets/images/logo.png`.

You can customize the favicon rendered in the browser tab by adding your own file to `app/assets/images/favicon.png`.

## Roadmap

- [ ] Support for article keyword lists
- [ ] Full authentication system beyond basic auth
- [ ] Commenting system
- [ ] Update UI to Hotwire
- [ ] Rake tasks to copy views from engine
- [ ] Improved customization options and splitting into additional partials
- [ ] Rework CSS classes to allow using frameworks other than Bootstrap

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).
