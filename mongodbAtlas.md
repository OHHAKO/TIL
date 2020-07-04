```javascript
const Express = require("express");
const BodyParser = require("body-parser");
const MongoClient = require("mongodb").MongoClient;

var app = Express();
const OPTION = { useUnifiedTopology: true };

//connect 1 - URL 연결, OPTION 추가설정, RESULT에서 error, client 잡기
MongoClient.connect(CONNECTION_URL, OPTION, RESULT);

//connect 2. - URL 연결,  OPTION 추가설정
const client = new MongoClient(CONNECTION_URL, OPTION);

client.connect(function (error, db) {
  if (error) {
    console.log(error);
  } else {
    console.log("connected:" + db);
  }
});
```
