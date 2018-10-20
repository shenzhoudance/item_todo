# 基本功能体系
https://www.youtube.com/watch?v=Z9GaNNztcZk&t=114s
https://github.com/RyanHemrick/Checklist/blob/master/app/controllers/items_controller.rb

cd newspace
ls
cd checklist

git checkout -b Item
rails g model Item title:string description:text
atom .
rake db:migrate

rails g controller Items
bundle install
rails generate simple_form:install --bootstrap

rails s
git add .
git commit -m "add_items"
git remote add origin https://github.com/shenzhoudance/item_todo.git
git push origin Item

---
