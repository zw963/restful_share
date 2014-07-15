# **RESTful** #
# and #
# **named route** #

----

# What is __RESTful__?

---

## I can't speak clearly.
 
---

##  RESTful __practice__ in Rails.

---

### Why we want to use RESTful?

-  Rails __convention__.
   
-  Make things __simple__.

- It __configurable__.
  
---

## start __practice__ right now!
<br />

    $ git clone https://github.com/zw963/restful_share
    
    $ bundle && rake db:migrate
    
    $ rails s

---

## what's we __have__ now ?
<br />

    $ rails g scaffold product name:string price:integer

<br />

- app/models/product.rb. (singular)
- app/controllers/products_controller with *CRUD* actions (plural)
  
- app/views/products/*.html.slim
<br />

---

## That's __ALL__ things?

---

## No, we still need __route__ let them go to work together.

- we need routes

---

## we write all routes __manually__.

---

### We use the __scope__ to simplify the route.

We hope use http://website/<strong>products</strong>/*.html to access all pages.

we hope use **products**_controller to handle request.
<br />
<br />

## specification

    scope 'paths', controller: :controller do
      http_verb '/stripped_path' => :action_name, :as => 'named_route'
    end
    
---

### we need a product __list__.

    scope 'products', :controller => :products do
      get '/' => :index, :as => 'products'
    end

```sh
$ rake routes

products GET /products(.:format) products#index
```
## specification

named_route verb full_path(.:format) controller#action

---

### we need add a __new__ product

- first we need a __form__ to fill in product infomation.

        scope 'products', :controller => :products do
          get '/' => :index, :as => 'products'
      >>  get '/new' => :new, :as => 'new_product'
        end
- when we submit, we need sent a __POST__ request.

        scope 'products', :controller => :products do
          get '/' => :index, :as => 'products'
      >>  post '/' => :create
          get '/new' => :new, :as => 'new_product'
        end

fantastic rails can deal with __association__ for us.

## new_product => products#create
---

### we need edit a exist product.

        scope 'products', :controller => :products do
          get '/' => :index, :as => 'products'
          post '/' => :create
          get '/new' => :new, :as => 'new_product'
      >>  get '/:id/edit' => :edit, :as => 'edit_product'
      >>  patch '/:id' => :update
        end

### we can see routes again.

    $ rake routes
    
       products  GET  /products(.:format)          products#index
                 POST /products(.:format)          products#create
     new_product GET  /products/new(.:format)      products#new
    edit_product GET  /products/:id/edit(.:format) products#edit
                 PUT  /products/:id(.:format)      products#update

I guess you know:
## edit_product => products#update
             
---
## last step:

### we need display/delete one product.

    scope 'products', :controller => :products do
        get '/' => :index, :as => 'products'
        post '/' => :create
        get '/new' => :new, :as => 'new_product'
        get '/:id/edit' => :edit, :as => 'edit_product'
    >>  get '/:id' => :show, :as => 'product'
        put '/:id' => :update
    >>  delete '/:id' => :destroy
    end
---
### we get the whole __RESTful__ routes for product CRUD.

        products GET    /products(.:format)          products#index
                 POST   /products(.:format)          products#create
     new_product GET    /products/new(.:format)      products#new
    edit_product GET    /products/:id/edit(.:format) products#edit
         product GET    /products/:id(.:format)      products#show
                 PUT    /products/:id(.:format)      products#update
                 DELETE /products/:id(.:format)      products#destroy

## Any Question?

---

# OK! You got it.

## but, It's tedious for write so __many__ code.

---

### It's Rails, you no need to do like that.

### You can use __resources__

    resources :products

---

### go farther, you can customize it.

    resources :products, :only => [:index, :new, :create]

```sh
$ rake routes

       products GET  /products(.:format)     products#index
                POST /products(.:format)     products#create
    new_product GET  /products/new(.:format) products#new
```

---

### more farther, you can extend it.

    resources :products do
      member do
        get 'lock'
        get 'unlock'
      end
      collection do
        get '/' => :default
      end
    end

```sh
$rake routes

      lock_product GET    /products/:id/lock(.:format)   products#lock
    unlock_product GET    /products/:id/unlock(.:format) products#unlock
          products GET    /products(.:format)            products#default
      ...
```
---

### more more farther, resources can be nest.
    resource :posts do
      resource :comments
    end

```sh

     post_comments GET    /posts/:post_id/comments(.:format)          comments#index
                  POST   /posts/:post_id/comments(.:format)          comments#create
 new_post_comment GET    /posts/:post_id/comments/new(.:format)      comments#new
edit_post_comment GET    /posts/:post_id/comments/:id/edit(.:format) comments#edit
     post_comment GET    /posts/:post_id/comments/:id(.:format)      comments#show
                  PUT    /posts/:post_id/comments/:id(.:format)      comments#update
                  DELETE /posts/:post_id/comments/:id(.:format)      comments#destroy
            posts GET    /posts(.:format)                            posts#index
                  POST   /posts(.:format)                            posts#create
         new_post GET    /posts/new(.:format)                        posts#new
        edit_post GET    /posts/:id/edit(.:format)                   posts#edit
             post GET    /posts/:id(.:format)                        posts#show
                  PUT    /posts/:id(.:format)                        posts#update
                  DELETE /posts/:id(.:format)                        posts#destroy
```
---

# It's So __Cool__!

---

## But, there is still a problem.
## with my poor memory, I can't remember __long long__ url.

---
## We can use  __named route__.
---
###  Rails route not only __map__ outer url request to suitable controller action.
### and
### it can __generate__ url for us.
### with a rememberable, intuitive name.
---

### redirect_to a full url.
    def destroy
      @product = Product.find(params[:id])
      @product.destroy
    
      redirect_to products_url
    end

```sh  
  products GET    /products(.:format)            products#index
```

products => products_url

http://website/products
---
### insert a link tag in view template.

    tbody
      - @products.each do |product|
        tr
          td = product.name
          td = product.price
          td = link_to 'Edit', edit_product_path product

```sh
edit_product GET    /products/:id/edit(.:format) products#edit
```
edit\_product => edit\_product\_path

/products/1/edit

---

### rails utilize RESTful named route generate a corresponding form tag for us.

    = form_for @product do |f|
      .field
        = f.text_field :name
      .field
        = f.number_field :price
      .actions = f.submit 'Save'

```html
<form id="new_product" class="new_product" method="post" action="/products">
  <input id="product_name" type="text" name="product[name]"></input>
  <input id="product_price" type="number" name="product[price]"></input>
  <input type="submit" value="Save" name="commit"></input>
</form>
```

---

# Finish
