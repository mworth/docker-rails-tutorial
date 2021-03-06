#### Using directions from [The docker-compose tutorial on creating a set of containers for a Rails app](https://docs.docker.com/compose/rails/#define-the-project) mashed up with [a tutorial on building a toy rails app](https://www.railstutorial.org/book/toy_app) mashed up with non-trivial amounts of a priori git knowledge

run the following to create the initial containers:
```bash
~/src/docker-rails-repo $ docker-compose run web rails new . --force --database=postgresql
```

First, Compose builds the image for the web service using the Dockerfile. Then it runs rails new inside a new container, using that image. Once it’s done, you should have generated a fresh app.

If you are running Docker on Linux, the files rails new created are owned by root. This happens because the container runs as the root user. If this is the case, change the ownership of the new files.

```bash
~/src/docker-rails-repo $ sudo chown -R $USER:$USER .
```

If you are running Docker on Mac or Windows, you should already have ownership of all files, including those generated by rails new.

Now that you’ve got a new Gemfile, you need to build the image again. (This, and changes to the Gemfile or the Dockerfile, should be the only times you’ll need to rebuild.)

```bash
~/src/docker-rails-repo $ docker-compose build
```
Next, we'll need to update the toy app to use the 'db' container. We'll also need to change the database and username to align with the defaults set by the postgres image. These changes happen within the submodule:

```bash
~/src/docker-rails-repo $ ${EDITOR} config/database.yml
```

We'll add to the default config in database.yml - here's a diff:
```
 default: &default
   adapter: postgresql
   encoding: unicode
+  host: db
+  username: postgres
+  password:
   # For details on connection pooling, see Rails configuration guide
   # http://guides.rubyonrails.org/configuring.html#database-pooling
   pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
```

We can now boot the app with 'docker-compose up'!