# 3. Mostly Static Pages
- [ ] in vault

###### Chapter starting steps
- Make a new app, called [sample_app](https://github.com/exemplar-codes/rails-sample-app)
- Made a heroku [app](https://dashboard.heroku.com/apps/still-chamber-76811)
- Generate controller using scaffold, `rails generate controller StaticPages home help`, which generates a controller (based on camel case) and two actions in it. This also adds routes in `config/routes.rb`.

###### Rail command shorthands:
![](/assets/3_Mostly_Static_Pages-image-1.png)

###### Undoing Rails commands
- To undo a scaffold done via `rails generate`, run `rails destroy`. Examples:
	```bash
	rails generate controller StaticPages home help
	rails destroy  controller StaticPages home help

	rails generate model User name:string email:string
	rails destroy  model User
	```
- To undo migrations, run rollback
	```bash
	rails db:migrate
	rails db:rollback # undo

	# To go to the beginning, or some other version, use
	rails db:migrate VERSION=0
	```

###### Page template files
Controller actions (functions) when generated, are empty. In Ruby, an empty method would mean nothing is done. But in Rails, controller actions reside in a class that inherits from `ApplicationController::Base`, so actions actually do this:
- Run the method code.
- Render corresponding view file, which has access to instance variables from the code run (above).
Template files for views have the extension `.html.erb` and reside in `app/views/controller_name/action_name.html.rb`.
- There are two kinds of embedded Ruby constructs:
	1. Execute only - `<% code_here %>`
	2. Execute and insert - `<%= code_expression_here %>`
	All embedded Ruby parts behave like a single file.
Example:

```HTML.ERB
		<% x = 2 %>
		<p>X = <%= x * 10 %></p>
		<!-- X=20 is displayed-->
```
###### Application layout file
This ( `view/layout/application.html.erb`) file is the general view of any page of the website.
It does 4 things:
1. Adds CSS from the asset pipeline
2. Adds JavaScript from the asset pipeline
3. Adds security via
	1. CSP meta tag - mitigates cross-site scripting (XSS) attacks.
	2. CSRF meta tag - mitigates cross-site request forgery attacks.
4. Render HTML generated from relevant ERB file within it.

Note: Remember to remove outermost stuff like `html`, `DOCTYE` and the like from ERB files, when using the `view/layout/application.html.erb` file.
