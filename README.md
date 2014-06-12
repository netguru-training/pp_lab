pp_lab
======

### source

this is based on the content available on official Rails guides site (http://guides.rubyonrails.org/getting_started.html).


## development steps

### app setup

```shell
bundle install
rails generate controller welcome index
```

see `controllers/welcome_contorller.rb`

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

edit `controllers/articles_controller.rb`

```ruby
def index
end
```

add and edit `views/articles/index.html.haml`

```haml
%h1 listing articles
```

edit `controllers/articles_controller.rb`

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


### showing single article

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

### listing all articles

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

### adding new article

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

### validating record

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

### form for creating new records

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

### updating records

edit `controllers/articles_controller.rb`

```ruby
def edit
  @article = Article.find(params[:id])
end
```

create `app/views/articles/edit.html.haml`

```haml
%h1 Editing article
= form_for :article, url: article_path(@article), method: :patch do |f|
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

edit `controllers/articles_controller.rb`

```ruby
def update
  @article = Article.find(params[:id])

  if @article.update(article_params)
    redirect_to @article
  else
    render 'edit'
  end
end

private

def article_params
  params.require(:article).permit(:title, :text)
end
```

### destroying records

### adding associations

```shell
rails generate model Comment commenter:string body:text article:references
```

migrate

```shell
rake db:migrate
```

edit `app/models/article.rb`

```ruby
class Article < ActiveRecord::Base
  has_many :comments
  validates :title, presence: true,
                    length: { minimum: 5 }
end
```

edit `config/routes.rb`

```ruby
resources :articles do
  resources :comments
end
```

### adding comments to articles

```shell
rails generate controller Comments
```

edit `views/articles/show.html.haml`

```haml
%p
  %strong Title:
  = @article.title
%p
  %strong Text:
  = @article.text
%h2 Add a comment:
= form_for([@article, @article.comments.build]) do |f|
  %p
    = f.label :commenter
    %br/
    = f.text_field :commenter
  %p
    = f.label :body
    %br/
    = f.text_area :body
  %p
    = f.submit
= link_to 'Back', articles_path
= link_to 'Edit', edit_article_path(@article)
```

create `app/controllers/comments_controller.rb`

```ruby
class CommentsController < ApplicationController
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)
    redirect_to article_path(@article)
  end

  private

  def comment_params
    params.require(:comment).permit(:commenter, :body)
  end
end
```


edit `views/articles/show.html.haml`

```haml
%p
  %strong Title:
  = @article.title
%p
  %strong Text:
  = @article.text
%h2 Comments
- @article.comments.each do |comment|
  %p
    %strong Commenter:
    = comment.commenter
  %p
    %strong Comment:
    = comment.body
%h2 Add a comment:
= form_for([@article, @article.comments.build]) do |f|
  %p
    = f.label :commenter
    %br/
    = f.text_field :commenter
  %p
    = f.label :body
    %br/
    = f.text_area :body
  %p
    = f.submit
= link_to 'Edit Article', edit_article_path(@article)
= link_to 'Back to Articles', articles_path
```

### refactoring

#### partials

create partial `views/comments/_comment.html.haml`

```haml
%p
  %strong Commenter:
  = comment.commenter
%p
  %strong Comment:
  = comment.body
```

edit `views/articles/show.html.haml`

```haml
%p
  %strong Title:
  = @article.title
%p
  %strong Text:
  = @article.text
%h2 Comments
= render @article.comments
%h2 Add a comment:
= form_for([@article, @article.comments.build]) do |f|
  %p
    = f.label :commenter
    %br/
    = f.text_field :commenter
  %p
    = f.label :body
    %br/
    = f.text_area :body
  %p
    = f.submit
= link_to 'Edit Article', edit_article_path(@article)
= link_to 'Back to Articles', articles_path
```

create `views/comments/_form.html.haml`

```haml
= form_for([@article, @article.comments.build]) do |f|
  %p
    = f.label :commenter
    %br/
    = f.text_field :commenter
  %p
    = f.label :body
    %br/
    = f.text_area :body
  %p
    = f.submit
```

edit `views/articles/show.html.haml`

```haml
%p
  %strong Title:
  = @article.title
%p
  %strong Text:
  = @article.text
%h2 Comments
= render @article.comments
%h2 Add a comment:
= render "comments/form"
= link_to 'Edit Article', edit_article_path(@article)
= link_to 'Back to Articles', articles_path
```
