# Jekyll Static Site with GitHub Pages

## Steps to Set Up

### 1. Create a GitHub Pages Repository

Create a new repository named `your-username.github.io`. This will host your static site.

### 2. Install Ruby and Bundler

Ensure you have Ruby installed. You can check with:

```sh
ruby -v
```

If not installed, download it from [Ruby's official site](https://www.ruby-lang.org/en/downloads/).

Then install Bundler, which manages Ruby gems:

```sh
gem install bundler
```

### 3. Add a `Gemfile`

Create a `Gemfile` in your project root and add:

```ruby
source 'https://rubygems.org'

gem 'github-pages' # GitHub Pages support
gem 'jekyll-paginate'
gem 'html-proofer'
gem 'jekyll-relative-links'
gem 'jekyll-seo-tag'
gem 'jekyll-sitemap'
gem 'jekyll-feed'
gem 'jekyll-gist'
gem 'erb'
gem 'webrick', '~> 1.8' # Local server
```

### 4. Install Dependencies

Run:

```sh
bundle install
```

This installs all the required gems.

### 5. Clone This Repository

```sh
git clone https://github.com/your-username/your-repo.git
cd your-repo
```

### 6. Serve the Site Locally

```sh
bundle exec jekyll serve

# if you want to add future blogs
bundle exec jekyll serve --future
```

Your site will be available at `http://localhost:4000`.

## Modify the Template

- Add new Markdown (`.md`) files in the root directory to include them in the sidebar.
- Edit `_config.yml` to set site configuration.
- Customize `_includes` for the header and sidebar.
- Modify `_layouts` to change the rendering of `.md` files.
- Store blog posts in `_posts` and projects in `_projects`.

### Example to Loop Through Posts

```liquid
{% raw %}
{% for post in site.posts %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
{% endfor %}
{% endraw %}
```

This will list all blog posts dynamically.

## Deployment

Push your changes to the `main` branch of your GitHub repository, and your site will be live at `https://your-username.github.io/`.

---

Now you're ready to build and customize your Jekyll-powered static site!
