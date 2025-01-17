# Firefox Accounts database service

# Deprecated!

**This service is no longer used by auth-server and is in the process of being dismantled. DB migration `.sql` files should be the only new additions here for the time being. New DB API work should be done in `fxa-shared/db`.**

---

Node.js-based database service
for Firefox Accounts.
Includes:

- The [API server](#api-server).
- A [MySQL backend](#mysql-backend).
  Used in production.

## Prerequisites

- node.js 14
- npm
- MySQL (we use version 5.6.42 in production)

## Testing

This package uses [Mocha](https://mochajs.org/) to test its code. By default `npm test` will run all NPM test scripts and then lint the code:

- `npm run test-mysql` will test database code under `test/backend` and `test/local`.
- `npm run test-server` will test server code under `db-server/test/local`.

Test specific tests with the following commands:

```bash
# Test only test/lib/log.js
./scripts/mocha-coverage.js test/lib/log.js

# Grep for "error module" under db-server/test
./scripts/mocha-coverage.js db-server/test/*/** -g "error module"
```

Refer to Mocha's [CLI documentation](https://mochajs.org/#command-line-usage) for more advanced test configuration.

## API Server

See the [API documentation][apidocs].
Backend implementers should also read
the [database documentation][dbdocs].

To run the server tests:

```sh
npm run test-server
```

## MySQL backend

Implements the [backend API][dbdocs]
as a MySQL database.

To run the MySQL tests:

```sh
npm run test-mysql
```

### Configuration

Both the server
and the database patcher
read values from a config file
`config/$NODE_ENV.json`,
where `NODE_ENV` is an environment variable
set in the shell.

For local development,
set `NODE_ENV` to `dev`
then create a new JSON file
called `config/dev.json`.
In there,
you can set any values
that you'd like to override
the master config file,
`config/config.js`.

For instance:

```json
{
  "master": {
    "user": "root",
    "password": "foo"
  },
  "slave": {
    "user": "root",
    "password": "bar"
  }
}
```

### Starting the server

You can start the server like so:

```sh
npm start
```

This will set up the database for you
then start the server on whichever port
is configured in `config/$NODE_ENV.json`
(port 8000 by default).

If the server fails to start,
check that MySQL is running
and that your active config
has the correct settings
to connect to the database.

### Setting-up the database separately

If you want to run
the database patcher on its own,
use the following command:

```sh
node bin/db_patcher.js
```

This command creates the database
if it doesn't exist,
then runs migrations
from `lib/db/schema`
in the appropriate order.
Both forward and reverse migrations
are contained in this directory,
but note that the reverse migrations
are commented out
as a precaution against
accidental execution.

If the command fails,
check that MySQL is running
and that your active config
has the correct settings
to connect to the database.

### Clean-up

If you want to clean the database,
just drop it in MySQL:

```sh
mysql -u root -p -e 'DROP DATABASE fxa'
```

It will be recreated automatically
next time you run `npm start`.

## License

[MPL 2.0][license]

[apidocs]: docs/API.md
[dbdocs]: docs/DB_API.md
[server-readme]: db-server/README.md
[license]: LICENSE
