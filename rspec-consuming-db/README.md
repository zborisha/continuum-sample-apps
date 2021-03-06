### rspec-consuming-db

This application exists to demonstrate Continuum's capability to create and bind
to services on demand while an application is staging.

The continuum.conf file specifies the type of service which is required by the stager
that deploys your application. The database.yml file interpolates the binding
environment variable (POSTGRES_URI) to acquire the database credentials.

1. Policy and the ruby version

  This ruby application requires Ruby version 2.0.0 or better.  In this case
  we have decided not to specify the ruby version in the Gemfile.  Instead
  we are going to let the Apcera policy engine use a newer version of Ruby. To
  add the policy to achieve this, execute the following command:

```console
$ apc policy import rubyVersionPolicy.pol
```

  Verify the policy:

```console
  $ apc policy show rubyVersionPolicy
```

  Verify that this looks right, then create the application:

2. Create the application

```console
  $ apc app create rspec-consuming-db
```

3. Create a provider for the app
  This app does not yet have a database to serve it. It is written to use
  a local sqlite instance so it works without it, but lets bind to a postgres
  database service.

```console
  $ apc providers list
  +----------+-----------+-------------+
  | Name     | Namespace | Description |
  +----------+-----------+-------------+
  | postgres | /         |             |
  +----------+-----------+-------------+
```

  The next step is to create a service for that provider. If one already exists which you
  want to use, then skip this step:

```console
  $ apc service create db-for-rspec --provider postgres
```

4. Next bind the service to our application:

```console
  $ apc service bind mydb1 --app todo
```

5. Finally start the application.

```console
  $ apc app start rspec-consuming-db
  Waiting for the job to start...
  [stdout] => Booting WEBrick
  [stdout] => Rails 4.0.2 application starting in production on http://0.0.0.0:4000
  [stdout] => Run `rails server -h` for more startup options
  [stdout] => Ctrl-C to shutdown server
  [stderr] [2014-06-13 17:22:03] INFO  WEBrick 1.3.1
  [stderr] [2014-06-13 17:22:03] INFO  ruby 2.1.0 (2013-12-25) [x86_64-linux]
  [stderr] [2014-06-13 17:22:03] INFO  WEBrick::HTTPServer#start: pid=3 port=4000
  Job should be accessible at "http://rspec-consuming-db.apcera.me:8181"
  Success!
```
5. Finally connect with a capsule and examine the application.

```console
  $ apc app console rspec-consuming-db
```

  Look /app/config/database.yml.  It should be using a postgress adapter.
