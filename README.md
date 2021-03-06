# T100 API V1

## API calls

Start Recording
```
/api/hsfb/v1/start
```
---
Stop Recording

```
/api/hsfb/v1/stop
```
---

Toggles Recording On/Off

```
/api/hsfb/v1/toggle
```
---

Returns Recording Status

```
/status
```
---

Delete All Clips on T100

```
/deleteVideos
```

---
Returns T100 Software Version

```
/version
```
---
Returns JSON object containing all clips and meta data
```
/allVideos.json
```

---------------------------------------------------------

## C# Send Command

Import
```c#
using System.Net;
```

Send Function
```c#
string t100ipAddress = "192.168.3.1";
string apiCall = "/api/hsfb/v1/toggle";

using (WebClient wc = new WebClient())
{
  string url = "http://" + t100ipAddress + ":1612" + apiCall;
  var json = wc.DownloadString(url);
  //JSON Response from server available in the "json" object
}
```

## Javascript Send Command
```html
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.2/jquery.min.js"></script>

<script>

  var t100ipAddress = "192.168.3.1";
  var apiCall = "/api/hsfb/v1/toggle";

  $.ajax({
    url: "http://" + t100ipAddress + ":1612" + apiCall,
    context: document.body
  }).done(function(data) {
    //Your Code here
    //Data returned from the server is available in the "data" object
  });

</script>
```

---------------------------------------------------------
## C# Discover T100

*This Code will return the IP Address of any T100s on the same local network*

Import
```c#
using System.Net;
using System.Net.Sockets;
```

Discovery Code
```c#
UdpClient client = new UdpClient();
client.ExclusiveAddressUse = false;
IPEndPoint localEp = new IPEndPoint(IPAddress.Any, 1612);
client.Client.SetSocketOption(SocketOptionLevel.Socket, SocketOptionName.ReuseAddress, true);
client.Client.Bind(localEp);
IPAddress multicastaddress = IPAddress.Parse("224.0.0.1");
client.JoinMulticastGroup(multicastaddress);

while (true) {
  Byte[] data = client.Receive(ref localEp);
  string strData = Encoding.UTF8.GetString(data); //Print strData to see full broadcast
  int start = strData.IndexOf("server");
  if(start > 0) {
    string server = strData.Substring(start).Split('"')[2]; //Check for IP Address In JSON String
    Console.WriteLine(server);
  }  
}
```

## Javascript Discover T100 (Node JS)

*This Code will return the IP Address of any T100s on the same local network*
```javascript
var dgram = require('dgram');
var udp = dgram.createSocket('udp4');
udp.bind(1612, function() {
  udp.addMembership('224.0.0.1');
});
udp.on('message', function(msg, rinfo) {
  try {
    var t100ipAddress = JSON.parse(msg).server;
    console.log(t100ipAddress);
    //Your Code here

  } catch (error) {
    err = error;
    return console.log("Bad Multicast Packet, skipping");
  }
});
```

