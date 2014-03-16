Lifesum Tech Blog
=================

### 1 Install jekyll

```
gem install jekyll
````

Then Download lifesum.gihtub.io to your local env

### 2 Start Server

```
jekyll serve
# => A development server will run at http://localhost:4000/
```
```
$ jekyll serve --detach
```

```
$ jekyll serve --watch
# => Same as `jekyll serve`, but watch for changes and regenerate automatically.
```

### 3 Creating a new post

You might have to run "bundle install" for this to work.

Creating new blog post: 

```
thor jekyll:new The title of the new post
```

Create new post and open in editor
```
thor jekyll:new The title of the new post --editor=vim
```




