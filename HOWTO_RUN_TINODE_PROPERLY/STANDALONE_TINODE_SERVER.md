## How to run a tinode server

1. Clone the repository
```
git clone https://github.com/haikalfachri/tinode-chat.git
```

2. Initialize the database
```
cd tinode-chat/tinode-db/
./tinode-db -config=tinode.conf -data=data.json
```

3. Run the server
```
cd ../server/
./tinode-server -config=tinode.conf -static_data=../frontend/webapp/
```

4. Test the server in port `:6060`