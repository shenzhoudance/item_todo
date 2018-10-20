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
