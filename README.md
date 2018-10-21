# 基本功能体系
https://www.youtube.com/watch?v=Z9GaNNztcZk&t=114s
https://github.com/RyanHemrick/Checklist/blob/master/app/controllers/items_controller.rb
```
cd newspace
ls
cd checklist
```
---
```
git checkout -b Item

rails g model Item title:string description:text
rails g controller
atom .
rake db:migrate
```
---
```
gem 'simple_form', '~> 4.0', '>= 4.0.1'
gem 'bootstrap-sass', '~> 3.3', '>= 3.3.7'

bundle install

rails generate simple_form:install --bootstrap

rails s
```
---
```
git add .

git commit -m "add_items"

git remote add origin https://github.com/shenzhoudance/item_todo.git

git push origin Item
```
---

config/routes.rb
```
Rails.application.routes.draw do
  resources :items
  root 'items#index'
end

```
app/controllers/items_controller.rb
```
class ItemsController < ApplicationController
   before_action :find_item, only: [:show, :edit, :update, :destroy]

   def index
     @items = Item.all.order("created_at DESC")
  end

   def show
  end


  def new
    @item = Item.new
  end

  def create
    @item = Item.new(item_params)
   if @item.save
     redirect_to root_path
   else
     render 'new'
   end
end

def edit
	end

	def update
		if @item.update(item_params)
			redirect_to root_path
		else
			render 'edit'
		end
	end

	def destroy
		@item.destroy
		redirect_to root_path
	end


  private

  def item_params
    params.require(:item).permit(:title, :description)
 end

 def find_item
   @item = Item.find(params[:id])
end

end

```
app/views/items/-form.html.erb
```
<%= simple_form_for @item do |f| %>
	<%= f.input :title, label: "Item" %>
	<%= f.input :description %>
	<%= f.button :submit %>
<% end %>

```
app/views/items/index.html.erb
```
<% @items.each do |item| %>
<h2><%= link_to item.title, item %></h2>
<p><%= item.description %></p>
<% end %>

<%= link_to "New item", new_item_path %>

```
app/views/items/show.html.erb
```
<h2><%= @item.title %></h2>
<p><%= @item.description %></p>

<%= link_to "Edit", edit_item_path(@item) %>
<%= link_to "Delete", item_path(@item), method: :delete, data: { confirm: "Are you sure?" } %>
<%= link_to "Back", root_path %>

```
app/views/items/new.html.erb
```
<div class="col-md-6 col-md-offset-3">
	<h1>New Checklist Item</h1>

	<%= render 'form' %>
</div>

```
app/views/items/edit.html.erb
```
<div class="col-md-6 col-md-offset-3">
	<h1>Edit Checklist Item</h1>

	<%= render 'form' %>
</div>

```
git checkout -b add_devise
rails g devise:install
rails g devise:views
rails g devise User
rake db:migrate
rails s
rails g migration add_user_id_to_items user_id:integer
rake db:migrate
rails s
```
app/controllers/items_controller.rb
```
class ItemsController < ApplicationController
   before_action :find_item, only: [:show, :edit, :update, :destroy]
   before_action :authenticate_user!, except: [:index]

   def index
     if user_signed_in?
     @items = Item.where(:user_id => current_user.id).order("created_at DESC")
   end
  end

   def show
  end


  def new
    @item = current_user.items.build
  end

  def create
    @item = current_user.items.build(item_params)
   if @item.save
     redirect_to root_path
   else
     render 'new'
   end
end

def edit
	end

	def update
		if @item.update(item_params)
			redirect_to root_path
		else
			render 'edit'
		end
	end

	def destroy
		@item.destroy
		redirect_to root_path
	end

  def complete
		@item = Item.find(params[:id])
		@item.update_attribute(:completed_at, Time.now)
		redirect_to root_path, notice: "Item successfully completed!"
	end

  private

  def item_params
    params.require(:item).permit(:title, :description)
 end

 def find_item
   @item = Item.find(params[:id])
end

end

```
```

rails c
2.3.1 :002 > Item.connection
2.3.1 :003 > Item
2.3.1 :004 > @item = Item.last
2.3.1 :007 > @item.user_id = 1
2.3.1 :008 > @item.save
2.3.1 :009 > @item = Item.find(2)
2.3.1 :010 > @item.user_id = 1
2.3.1 :011 > @item.save
2.3.1 :012 > @item = Item.find(3)
2.3.1 :013 > @item.user_id = 1
2.3.1 :014 > @item.save
2.3.1 :015 > exit


rails g migration add_completed_at_to_items completed_at:datetime
rake db:migrate

config/routes.rb
```
Rails.application.routes.draw do
  devise_for :users
  resources :items do
  	member do
  		patch :complete
  	end
  end
  root 'items#index'
end

```
app/views/items/-static_landing_page.html.erb
```
<div class="jumbotron">
	<h2>Welcome to your own Checklist!</h2>
	<h3>Keep track of stuff.</h3>
	<%= link_to "Sign Up", new_user_registration_path, class: "btn btn-primary" %>
	<%= link_to "Log In", new_user_session_path, class: "btn btn-primary" %>
</div>

```
app/views/items/index.html.erb
```
<% if user_signed_in? %>
  <% @items.each do |item| %>
  <h2><%= link_to item.title, item %></h2>
  <p><%= item.description %></p>
  <p><%= link_to "Mark as Complete",complete_item_path(item), method: :patch %></p>
  <p><%= link_to "Delete", item, method: :delete, data: { confirm: "Are you sure?" } %></p>
  <% end %>
  <% else %>
  <%= render 'static_landing_page' %>
<% end %>

```

app/models/user.rb
```
has_many :items
```
app/models/item.rb
```
class Item < ApplicationRecord
  belongs_to :user

  def completed?
		!completed_at.blank?
	end

end
```
rails c
2.3.1 :001 > @item = Item.last
2.3.1 :002 > @item = Item.last
2.3.1 :003 > exit


app/views/items/index.html.erb
```
<% if user_signed_in? %>
	<div class="col-md-6 col-md-offset-3">
		<% @items.each do |item| %>
			<div class="index-wrapper">
				<% if item.completed? %>
					<div class="row item">
						<div class="col-md-8">
							<h2 style="opacity: 0.5;" class="item-title"><strike><%= link_to item.title, item %></strike></h2>
							<p style="opacity: 0.5;" class="item-sub-title item-desc"><%= item.description %></p>
							<p style="opacity: 0.5;" class="time-created-at item-sub-title">Completed <%= time_ago_in_words(item.completed_at) %> ago</p>
						</div>
						<div class="col-md-2">
							<p><%= link_to complete_item_path(item), method: :patch do %></p>
								<i class="fa fa-check fa-lg"></i>
							<% end %>
						</div>
						<div class="col-md-2">
							<p><%= link_to item, method: :delete, data: { confirm: "Are you sure?" } do %></p>
								<i class="fa fa-trash fa-lg"></i>
							<% end %>
						</div>
					</div>
				<% else %>
					<div class="row item">
						<div class="col-md-8">
							<h2 class="item-title"><%= item.title %></h2>
							<p class="item-sub-title item-desc"><%= item.description %></p>
							<p style="opacity: 0.5;" class="time-created-at item-sub-title">Created <%= time_ago_in_words(item.created_at) %> ago</p>
						</div>
						<div class="col-md-2">
							<p><%= link_to complete_item_path(item), method: :patch do %></p>
								<i class="fa fa-check fa-lg"></i>
							<% end %>
						</div>
						<div class="col-md-2">
							<p><%= link_to item, method: :delete, data: { confirm: "Are you sure?" } do %></p>
								<i class="fa fa-trash fa-lg"></i>
							<% end %>
						</div>
					</div>
				<% end %>
			</div>

		<% end %>
	</div>
<% else %>
	<%= render 'static_landing_page' %>
<% end %>

```
app/assets/stylesheets/application.scss
```
@import "bootstrap-sprockets";
@import "bootstrap";

/* Mixins, variables, etc... */

@mixin nav_link_hover {
 transition: 0.4s;
 color: lighten(#222, 30%);
 text-decoration: none;
}

/* Universal styles */

body {
 font-family: 'Merriweather', serif;
}

a {
 color: darken(#e7e7e7, 70%);
}

/* Navbar and navigation styles */

.navbar {
 font-family: 'Oswald', sans-serif;
 text-transform: uppercase;
}

.navbar-default {

 padding-top: 5px;
 padding-bottom: 5px;
 background-color: #fff;

 .navbar-nav > li > a {
   color: #222;
 }

 .navbar-link {
   color: #222;
 }

 .navbar-brand {
   color: #222;
   letter-spacing: 1.25px;
 }

}

/* Item styles */

.item {
 border-bottom: 1px solid #e7e7e7;
 padding-bottom: 1.5em;
 .item-title {
   font-family: 'Oswald', sans-serif;
   margin-top: 0;
 }
 .item-desc {
   font-size: 1em;
 }
 .time-ago {
   color: darken(#e7e7e7, 50%);
   font-size: 0.85em;
   letter-spacing: 1.25;
 }
 .item-sub-title {
   margin-bottom: 0;
 }
}

.item:nth-child(n+1) {
 padding-top: 1.5em;
}
```
