## Making real time API with mongodb in express server

## Frontend can be in react or flutter

<details>
<summary> I want to use express js and mongodb in node js for backend and flutter for front-end in app Write code to implement realtime api fetching</summary>

Sure, here's an example of how to implement a realtime API fetching using Express.js and MongoDB in Node.js:

1.  First, make sure to install the necessary packages:

`npm install express mongoose cors`

2.  Set up your Express.js server and connect to your MongoDB database:

```javascript
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");

const app = express();

app.use(express.json());
app.use(cors());

mongoose
  .connect("mongodb://localhost/mydatabase", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => {
    console.log("Connected to MongoDB!");
  })
  .catch((err) => {
    console.error("Error connecting to MongoDB:", err);
  });

const port = 3000;

app.listen(port, () => {
  console.log(`Server listening on port ${port}...`);
});
```

3.  Create a MongoDB schema and model for your data:

```javascript
const mongoose = require("mongoose");

const myDataSchema = new mongoose.Schema({
  name: { type: String, required: true },
  description: { type: String, required: true },
});

const MyData = mongoose.model("MyData", myDataSchema);

module.exports = MyData;
```

4.  Create an API endpoint to fetch your data in real-time:

```javascript
const express = require("express");
const MyData = require("./myData");

const router = express.Router();

router.get("/mydata", (req, res) => {
  MyData.find()
    .then((data) => {
      res.json(data);
    })
    .catch((err) => {
      console.error("Error fetching data:", err);
      res.status(500).json({ error: "Error fetching data" });
    });
});

module.exports = router;
```

5.  Finally, import your API endpoint into your Express.js server and use it:

```javascript
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");
const myDataRouter = require("./myDataRouter");

const app = express();

app.use(express.json());
app.use(cors());

mongoose
  .connect("mongodb://localhost/mydatabase", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => {
    console.log("Connected to MongoDB!");
  })
  .catch((err) => {
    console.error("Error connecting to MongoDB:", err);
  });

app.use("/api", myDataRouter);

const port = 3000;

app.listen(port, () => {
  console.log(`Server listening on port ${port}...`);
});
```

Now you can use this endpoint in your Flutter app to fetch data in real-time:

```dart
import 'package:http/http.dart' as http;
import 'dart:convert';

Future<List<dynamic>> fetchData() async {
  final response = await http.get(Uri.parse('http://localhost:3000/api/mydata'));
  final data = json.decode(response.body);
  return data;
}
```

Note: This example code is for demonstration purposes only and may need to be adapted to fit your specific use case.

</details>

<details>
<summary> But this will fetch only once i want to listen whenever database changes</summary>

To listen for changes to the database in real-time, you can use MongoDB's change streams feature. Here's an example of how to modify the previous code to use change streams:

1.  First, modify the MyData schema to include a timestamp field:

```javascript
const myDataSchema = new mongoose.Schema({
  name: { type: String, required: true },
  description: { type: String, required: true },
  timestamp: { type: Date, default: Date.now },
});
```

2.  Next, create a change stream in your API endpoint:

```javascript
router.get("/mydata", (req, res) => {
  const changeStream = MyData.watch();

  changeStream.on("change", (change) => {
    console.log("Change detected:", change);
    sendData();
  });

  const sendData = () => {
    MyData.find()
      .then((data) => {
        res.json(data);
      })
      .catch((err) => {
        console.error("Error fetching data:", err);
        res.status(500).json({ error: "Error fetching data" });
      });
  };

  sendData();
});
```

This code creates a change stream for the MyData collection and listens for changes. When a change is detected, it calls the `sendData()` function to fetch the updated data from the database and send it to the client.

3.  Finally, modify the `fetchData()` function in your Flutter app to listen for changes using a WebSocket:

```dart
import 'package:web_socket_channel/io.dart';

Stream<List<dynamic>> listenForData() {
  final channel = IOWebSocketChannel.connect('ws://localhost:3000/api/mydata');

  return channel.stream.map((data) => json.decode(data));
}
```

This code creates a WebSocket connection to the `/api/mydata` endpoint and returns a `Stream` that emits data whenever a change is detected in the database.

Note: In addition to modifying the code as described above, you'll also need to install the `ws` package in your Express.js server to enable WebSocket support. You can do this by running `npm install ws` and adding the following code to your server:

```javascript
const http = require("http");
const WebSocket = require("ws");

const server = http.createServer(app);
const wss = new WebSocket.Server({ server, path: "/api/mydata" });

wss.on("connection", (ws) => {
  console.log("WebSocket connected");

  ws.on("close", () => {
    console.log("WebSocket disconnected");
  });
});
```

</details>

<details>
<summary> Use listenForData() function in a flutter widget to show data in realtime. Write only required code</summary>

Here's an example of how to use the `listenForData()` function to show data in real-time in a Flutter widget:

```dart
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:web_socket_channel/io.dart';

class RealtimeDataWidget extends StatefulWidget {
  @override
  _RealtimeDataWidgetState createState() => _RealtimeDataWidgetState();
}

class _RealtimeDataWidgetState extends State<RealtimeDataWidget> {
  Stream<List<dynamic>> _dataStream;

  @override
  void initState() {
    super.initState();

    _dataStream = listenForData();
  }

  @override
  Widget build(BuildContext context) {
    return StreamBuilder<List<dynamic>>(
      stream: _dataStream,
      builder: (context, snapshot) {
        if (snapshot.hasError) {
          return Text('Error fetching data');
        }

        if (!snapshot.hasData) {
          return CircularProgressIndicator();
        }

        final data = snapshot.data;

        return ListView.builder(
          itemCount: data.length,
          itemBuilder: (context, index) {
            final item = data[index];

            return ListTile(
              title: Text(item['name']),
              subtitle: Text(item['description']),
            );
          },
        );
      },
    );
  }
}

Stream<List<dynamic>> listenForData() {
  final channel = IOWebSocketChannel.connect('ws://localhost:3000/api/mydata');

  return channel.stream.map((data) => json.decode(data));
}
```

This code creates a `RealtimeDataWidget` that uses the `StreamBuilder` widget to build a list of items based on the data received from the `listenForData()` function.

In the `initState()` method, the `_dataStream` variable is initialized with the stream returned by `listenForData()`.

In the `build()` method, the `StreamBuilder` widget is used to build the list of items. If an error occurs while fetching data, an error message is displayed. If data is still being fetched, a `CircularProgressIndicator` is displayed. Otherwise, the data is displayed in a `ListView`.

Note: In order to use this widget, you'll need to import the `dart:convert` package and add the `web_socket_channel` package to your pubspec.yaml file.

</details>

<details>
<summary> Write code in react also to call at same api, fetch data in realtime and show it</summary>

Here's an example of how to use `WebSocket` in React to fetch data in real-time from the same API and display it:

```jsx
import React, { useState, useEffect } from "react";
import { WebSocket } from "socket.io-client";

const RealtimeData = () => {
  const [data, setData] = useState([]);
  const [error, setError] = useState("");

  useEffect(() => {
    const socket = new WebSocket("ws://localhost:3000/api/mydata");

    socket.onmessage = (event) => {
      const jsonData = JSON.parse(event.data);
      setData(jsonData);
    };

    socket.onerror = (event) => {
      setError("Error fetching data");
    };

    return () => {
      socket.close();
    };
  }, []);

  if (error) {
    return <div>{error}</div>;
  }

  if (!data.length) {
    return <div>Loading...</div>;
  }

  return (
    <ul>
      {data.map((item) => (
        <li key={item._id}>
          <div>{item.name}</div>
          <div>{item.description}</div>
        </li>
      ))}
    </ul>
  );
};

export default RealtimeData;
```

This code uses the `WebSocket` class from the `socket.io-client` package to create a WebSocket connection to the `/api/mydata` endpoint on the server.

When data is received from the server, it is parsed from JSON and stored in the `data` state variable using the `setData()` function.

If an error occurs while fetching data, the `setError()` function is used to set the `error` state variable.

The `useEffect()` hook is used to create the WebSocket connection when the component mounts, and to close the connection when the component unmounts.

In the `return` statement, the `error` and `data` state variables are checked to display the appropriate content. If there is an error, the error message is displayed. If data is still being fetched, a loading message is displayed. Otherwise, the data is displayed in a list.

Note: In order to use this code, you'll need to import the `socket.io-client` package using `npm` or `yarn`. You'll also need to make sure that your server is configured to allow WebSocket connections.

</details>

<details>
<summary> Is it secure to use id in key of map elements. Can someone use this id to change database</summary>

It is generally considered safe to use the `id` of a database record as the key of a map element in a React component, as long as you take certain precautions to prevent malicious users from modifying the database.

Firstly, you should make sure that you don't expose any sensitive data in your React component. For example, if your database contains user login credentials or other sensitive information, you should not include that information in your React component. Instead, you should only include the data that is needed to display the UI.

Secondly, you should validate any user input that is sent to your server to prevent injection attacks or other security vulnerabilities. For example, if your server allows users to update records in the database, you should validate that the user has permission to make the update, and that the data they are sending is valid and does not contain any malicious code.

Thirdly, you should make sure that your server is properly secured and configured to prevent unauthorized access. This

</details>
