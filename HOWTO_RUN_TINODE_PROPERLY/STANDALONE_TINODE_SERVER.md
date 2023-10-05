## Building from Source

1. Install [Go environment](https://golang.org/doc/install). The installation instructions below are for Go 1.16 and newer.

2. OPTIONAL only if you intend to modify the code: Install [protobuf](https://developers.google.com/protocol-buffers/) and [gRPC](https://grpc.io/docs/languages/go/quickstart/) including [code generator](https://developers.google.com/protocol-buffers/docs/reference/go-generated) for Go.

3. Make sure one of the following databases is installed and running:
 * MySQL 5.7 or above. MySQL 5.6 or below **will not work**.
 * PostgreSQL 13 or above. PostgreSQL 12 or below **will not work**.
 * MongoDB 4.2 or above.
 * RethinkDB.

4. Fetch, build Tinode server and tinode-db database initializer:
  - **MySQL**:
	```
	go install -tags mysql github.com/tinode/chat/server@latest
	go install -tags mysql github.com/tinode/chat/tinode-db@latest
	```
  - **PostgreSQL**:
	```
	go install -tags postgres github.com/tinode/chat/server@latest
	go install -tags postgres github.com/tinode/chat/tinode-db@latest
	```
  - **MongoDB**:
	```
	go install -tags mongodb github.com/tinode/chat/server@latest
	go install -tags mongodb github.com/tinode/chat/tinode-db@latest
	```
  - **RethinkDb**:
	```
	go install -tags rethinkdb github.com/tinode/chat/server@latest
	go install -tags rethinkdb github.com/tinode/chat/tinode-db@latest
	```
  - **All** (bundle all of the above DB adapters):
	```
	go install -tags "mysql rethinkdb mongodb postgres" github.com/tinode/chat/server@latest
	go install -tags "mysql rethinkdb mongodb postgres" github.com/tinode/chat/tinode-db@latest
	```

    The steps above install Tinode binaries at `$GOPATH/bin/`, sorces and supporting files are located at `$GOPATH/pkg/mod/github.com/tinode/chat@vX.XX.X/` where `X.XX.X` is the version you installed, such as `0.19.1`.

    Note the required **`-tags rethinkdb`**, **`-tags mysql`**, **`-tags mongodb`** or **`-tags postgres`** build option.

    You may also optionally define `main.buildstamp` for the server by adding a build option, for instance, with a timestamp:
    ```
    go install -tags mysql -ldflags "-X main.buildstamp=`date -u '+%Y%m%dT%H:%M:%SZ'`" github.com/tinode/chat/server@latest
    ```
    The value of `buildstamp` will be sent by the server to the clients.

    Go versions 1.14 and 1.15 should use a combination of `go get` and `go build` to install Tinode, e.g.
    ```
    go get -tags mysql github.com/tinode/chat/server && go build -tags mysql -o $GOPATH/bin/tinode github.com/tinode/chat/server
    ```
    Building with Go 1.13 or below **will fail**!

5. Open `tinode.conf` (located at `$GOPATH/pkg/mod/github.com/tinode/chat@vX.XX.X/server/`). Check that the database connection parameters are correct for your database. If you are using MySQL make sure [DSN](https://github.com/go-sql-driver/mysql#dsn-data-source-name) in `"mysql"` section is appropriate for your MySQL installation. Option `parseTime=true` is required.
```js
	"mysql": {
		"dsn": "root@tcp(localhost)/tinode?parseTime=true",
		"database": "tinode"
	},
```

6. Make sure you specify the adapter name in your `tinode.conf`. E.g. you want to run Tinode with MySQL:
```js
	"store_config": {
		...
		"use_adapter": "mysql",
		...
	},
```

7. Now that you have built the binaries, follow instructions in the _Running a Standalone Server_ section.


## Running a Standalone Server

If you followed instructions in the previous section then the Tinode binaries are installed in `$GOPATH/bin/`, the sources and supporting files are located in `$GOPATH/pkg/mod/github.com/tinode/chat@vX.XX.X/`, where `X.XX.X` is the version you installed, for example `0.19.1`.

Switch to sources directory (replace `X.XX.X` with your actual version, such as `0.19.1`):
```
cd $GOPATH/pkg/mod/github.com/tinode/chat@vX.XX.X
```

1. Make sure your database is running:
 - **MySQL**: https://dev.mysql.com/doc/mysql-startstop-excerpt/5.7/en/mysql-server.html
	```
	mysql.server start
	```
 - **PostgreSQL**: https://www.postgresql.org/docs/current/app-pg-ctl.html
	```
	pg_ctl start
	```
 - **MongoDB**: https://docs.mongodb.com/manual/administration/install-community/
MongoDB should run as single node replicaset. See https://docs.mongodb.com/manual/administration/replica-set-deployment/
	```
	mongod
	```
 - **RethinkDB**: https://www.rethinkdb.com/docs/start-a-server/
	```
	rethinkdb --bind all --daemon
	```

2. Set DB config in ./tinode-db/tinode.conf to be the same as ./server/tinode.conf, run DB initializer
	```
	$GOPATH/bin/tinode-db -config=./tinode-db/tinode.conf
	```
	add `-data=./tinode-db/data.json` flag if you want sample data to be loaded:
	```
	$GOPATH/bin/tinode-db -config=./tinode-db/tinode.conf -data=./tinode-db/data.json
	```

	DB initializer needs to be run only once per installation. See [instructions](tinode-db/README.md) for more options.

3. Unpack JS client to a directory, for instance `$HOME/tinode/webapp/` by unzipping `https://github.com/tinode/webapp/archive/master.zip` and `https://github.com/tinode/tinode-js/archive/master.zip` to the same directory.

4. Copy or symlink template directory `./server/templ` to `$GOPATH/bin/templ`
	```
	ln -s ./server/templ $GOPATH/bin
	```

5. Run the server
	```
	$GOPATH/bin/server -config=./server/tinode.conf -static_data=$HOME/tinode/webapp/
	```

6. Test your installation by pointing your browser to [http://localhost:6060/](http://localhost:6060/). The static files from the `-static_data` path are served at web root `/`. You can change this by editing the line `static_mount` in the config file.

**Important!** If you are running Tinode alongside another webserver, such as Apache or nginx, keep in mind that you need to launch the webapp from the URL served by Tinode. Otherwise it won't work.