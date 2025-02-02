# 5. Filling in the layout
- [ ] in vault

###### Asset pipeline intro
- A **single** folder for all static files ("assets"), like images, CSS and other stuff.
The location is `app/assets/`.
- It has typewise subfolders - Images are stored in `/images` and stylesheets in `/stylesheets`, etc.
- All files here will automatically be included on the server, especially stylesheets.

###### Server's assets directory
- The asset directory in development is not the same as the asset directory used by the server. They are different.
- The server has all (types of) assets in a single folder, for easy access.
- All assets have a unique long alphanumeric string appended to their names, to avoid reusing browser-cached assets, generally.

###### ERb tag helper functions
- These are Ruby functions that return HTML output as string.
- Why: To add Ruby logic to plain HTML tags, which eases writing View (V in MVC) code.
- Because they evaulate to strings, helper functions can be nested.

###### Links in ERb using `link_to`
- The function `link_to` is used for inserting links.
- Why:
	- DRY for long URLs
	- Avoid hardcoding of links (impact of change reduction).
- Function template is `link_to(content, url [, options=Hash])`. Content may be string or other ERb helper functions.
- **Note, in Ruby, trailing hash argument need not have enclosing `{}` - continuing it as comma separated values is OK.  Example: `f(23, alt: 'pic', width: "23")`.
- The hash contains tag attributes in Hash form. e.g. CSS `:class`, `:id` and other attributes.

	Examples:
	```HTML
	<%= link_to "Home", '#' %>
	==> renders to <a href="#">Home</a>


	<%= link_to "Sign up now!", '#', class: "btn btn-lg btn-primary" %>
	==> <a href="#" class="btn btn-lg btn-primary">Sign up now!</a>


	<%= link_to image_tag("rails.svg", alt: "Rails logo", width: "200"), "https://rubyonrails.org/" %>
	==> <a href="https://rubyonrails.org/"> <img alt="Rails logo" width="200px" src="/assets/rails-<long-string>.svg">
		</a>
	```

###### Images in ERb using `image_tag`
- Embedded image tag helper.
- Function template: `image_tag(src_path [, options=Hash])`. The options hash has usual image attributes like `alt`, `width` etc, all having string values.
- As image is an asset, a unique string is appended by the server for client HTML.
- Example:
	```HTML
	image_tag("rails.svg", alt: "Rails logo", width: "200")
	==> <img alt="Rails logo" width="200px" src="/assets/rails-<long string>.svg">
	```

###### Partials
- Why partials:
	- Pages (ERb) files of an application almost always have common parts.
	- Code not related to View:
		- Some parts of ERb files can be shims (small piece of code that solve nasty edge cases).
		- Module imports and stuff.
	All these parts are not needed while writing View (V in MVC) code. So it's best to abstract them away.
	This problem is solved using "partials", which means part of (partial) code kept in a different file.
- Partial files are also ERb files. And there's no special syntax, for the callee file - Just cut and paste the code.
- Steps to use partial:
	1. Keep partial code in a file.
	2. Name the file starting with an underscore. e.g. `_header.html.erb`
	3. Use the `render` Rails helper. It's exactly like `#include` in C++. The function takes path of the partial with file's functional name(i.e. without the underscore or file extensions - just the `_`  name  `.html.erb`). Example: `/shared/_header.html.erb` --> `/shared/header`.
	4. Partials are looked at starting from `app/view`. Keep partial path after this.
	Example:
	```HTML
	In callee - _footer.html.erb
		<%= ERB_code here%>
		...
		...
		<%= ERB_code here%>
	In caller - main.html.erb
		...
		<%= render 'relative_path/footer' %>
		...
	```

- Working nature of partials:
	- The `app/view/layouts` folder is kind of a global partial, with the central caller file being `layouts/application.html.erb`.
	- When working with parts of the applications, non-global partials are kept in folders named `shared`. Alternatively using a global folder for partials or all partials in one - choice.

##### Intro to styling in Rails
###### Styling in Rails
- Rails uses Sass as the default language for stylesheets.
- Stylesheets reside in `app/assets/stylesheets/` directory.
- Global styles - stylesheets not related to a controller are global.
- Local styles:
	- Rails automatically generates a CSS file for each controller and keeps it in and as `app/asstes/stylesheets/controller_name.scss`.

###### Boostrap and custom CSS
- The Bootstrap library uses LESS as the styling language, but Rails only accepts SASS. So I'll use `bootstrap-sass` gem. This gem:
	- Converts Less to Sass, for Rails.
	- Provides the Bootstrap core files in Sass format to Rails.
	Install using by first adding `gem 'bootstrap-sass', '3.4.1` to `Gemfile` and then doing `bundle install`.
- Add all Less style code to a global file called `custom.scss`, from the [book](https://railstutorial.org).
- The site looks something like this after adding Bootstrap.
 ![](/assets/5_Filling_in_the_layout-image-1.png)

###### Intro to Sassy CSS
Sass (short for 'Sassy CSS') is a syntactical improvement over regular CSS. Sass is a language that compiles down to regular CSS.
It supports various constructs, including:
- Nesting - this makes styling intuitive. To indicate parent, write `&`.
- Variables - $ based variables, like BASH.
Example
```SCSS
.center {
text-align: center;
}

.center h1 {
	margin-bottom: 10px;
}


// becomes nested, to

.center {
	text-align: center;
	h1 {
		margin-bottom: 10px;
	}
}
```

```SCSS
#logo {
	float: left;
	margin-right: 10px;
}


#logo:hover {
	color: #fff; text-decoration: none;
}

// parent reference
#logo {
	float: left;
	margin-right: 10px;

	&:hover { // '&' references parent - #logo
		color: #fff;
		text-decoration: none; }
	}
}
```

```SCSS
// variables
h1 {
	...
	color: #777;
	...
}

// becomes
$light-gray: #777;

// variable naming rules - like C++ with hypens allowed.

h1 {
	...
	color: $light-gray
	...
}
```

###### Named URLs
- Add the links using `link_to`. But before using _named URLs_, I need to first make them available in `config/routes.rb`. Like so
	```ruby
	get 'static_pages/about' # the default value
	# link_to 'static_pages/about' - hardcoded

	# For named route, define a mapping to controller#action
	get '/about', to: 'static_pages#about'
	# named URLs can now be used
	# This gets rendered as
	link_to about_path # '/about', which is filled by routes.rb
	link_to about_url # the final URL. 'http://www.website_domain.tld/about'
	```
- Here's how Rails makes pages available without hardcoding - this minimizes impact of change. If a route changes, change the `routes.rb`, or if a link changes, just change the `link_to`. That's all, no hardcoding (although it's always available).

	![](/assets/5_Filling_in_the_layout-image-2.png)
- Note that the `get` value is rendered as the URL in the browser's omnibox.
- If I want to have an internal name for a really large URL. I can use an alias. Like so:
	```ruby
	get '/help', to: 'static_pages#help', as: 'helf'
	# helf_url and helf_path are available now.
	```
	Also, using an alias prevents me from using the named URL based on `get`. So `link_to help_url` will fail.
