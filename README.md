# kafka-stock-sim

Docker compose configuration that wraps the following
- Docker image for Kafka from Bitnami (which includes Zookeeper)
- Docker image for a stock update producer
- Docker image for a consumer

## Producer
The producer just writes messages to the 'stock' topic every 10s. 
The key to the message is just a sequential ID, the value is a JSON string
The JSON object contains a store code (limited to 3 stores at this point), a location code (again, 3 locations), an EAN and a random stock movement number (negative for removing stock, positive for recieving stock)

## Consumer

The consumer reads the stream of stock events, groups them by store code (so reads the XML) and then aggregates them to provide a table with two columns. The first, the key, is the Store Code + EAN (as a compound key). The second column is the stock value for that store / ean combination

The consumer exposes two API endpoints
<host>:9000/stores - doesn't actually return anything at the minute but check the logs and you'll see the results of the stock aggregation
<host>:9000/events - returns all the events since the last request (the code re-polls Kafka on each HTTP GET request)
