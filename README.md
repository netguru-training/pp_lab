pp_lab
======

```shell
bundle install
rails generate controller welcome index
```

see `controllers/welcome.rb`

edit `config/routes.rb`

```ruby
root 'welcome#index'
```

adding new controller

```shell
rails g controller articles
```

edit `config/routes.rb`

```ruby
resources :articles
```

edit `controllers/articles.rb`

```ruby
def index
end
```

add and edit `views/articles/index.html.haml`

```haml
%h1 listing articles
```

edit `controllers/articles.rb`

```ruby
def new
end
```

add and edit `views/articles/new.html.haml`

```haml
%h1 add new article
= form_for :article, url: articles_path do |f|
  %p
    = f.label :title
    %br/
    = f.text_field :title
  %p
    = f.label :text
    %br/
    = f.text_area :text
  %p
    = f.submit
```

edit `controllers/articles_controller.rb`

```ruby
def new
end
```

create `article` model

```shell
rails generate model Article title:string text:text
```

migrate db

```shell
rake db:migrate
```

edit `controllers/articles_controller.rb`

```ruby
def create
  @article = Article.new(article_params)

  @article.save
  redirect_to @article
end

private

def article_params
  params.require(:article).permit(:title, :text)
end
```

edit `controllers/articles_controller.rb`

```ruby
def show
  @article = Article.find(params[:id])
end
```

add and edit `views/articles/show.html.haml`

```haml
%p
  %strong Title:
  = @article.title
%p
  %strong Text:
  = @article.text
```

edit `controllers/articles_controller.rb`

```ruby
def index
  @articles = Article.all
end
```

edit `views/articles/index.html.haml`

```haml
%h1 Listing articles
%table
  %tr
    %th Title
    %th Text
  - @articles.each do |article|
    %tr
      %td= article.title
      %td= article.text
```

edit `views/welcome/index.html.haml`

```haml
= link_to 'My Blog', controller: 'articles'
```

edit `views/articles/new.html.haml`

```haml
%h1 add new article
= form_for :article, url: articles_path do |f|
  %p
    = f.label :title
    %br/
    = f.text_field :title
  %p
    = f.label :text
    %br/
    = f.text_area :text
  %p
    = f.submit
= link_to 'Back', articles_path
```

edit `views/articles/show.html.haml`

```haml
%p
  %strong Title:
  = @article.title
%p
  %strong Text:
  = @article.text
= link_to 'Back', articles_path
```

edit `models/article.rb`

```ruby
class Article < ActiveRecord::Base
  validates :title, presence: true,
                    length: { minimum: 5 }
end
```

edit `controllers/articles_controller.rb`

```ruby
def create
  @article = Article.new(article_params)

  if @article.save
    redirect_to @article
  else
    render 'new'
  end
end
```

edit `views/articles/new.html.haml`

```haml
= form_for :article, url: articles_path do |f|
  - if @article.errors.any?
    #error_explanation
      %h2
        = pluralize(@article.errors.count, "error")
        prohibited
        this article from being saved:
      %ul
        - @article.errors.full_messages.each do |msg|
          %li= msg
  %p
    = f.label :title
    %br/
    = f.text_field :title
  %p
    = f.label :text
    %br/
    = f.text_area :text
  %p
    = f.submit
= link_to 'Back', articles_path
```
