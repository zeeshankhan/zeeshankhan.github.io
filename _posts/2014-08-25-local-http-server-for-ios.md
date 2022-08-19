---
title: "Local HTTP Server for iOS"
date: "2014-08-25"
categories: 
  - "technical-posts"
tags: 
  - "cocoa-http-server"
  - "http-server"
  - "ios"
  - "localhost"
  - "max-os-x"
  - "networking"
---

## **Problem (Features)**

Yeah, because what I feel is, everything starts with a Problem, when we face problems, issues. We put our thoughts, we start to work on it, we come up with some solutions, we build something. Thats what we do. Right? Since we are in IT field, we are always into trouble :\], so many problems, so many issues, we solve one another comes up, sometimes they are so many. A big nerd have said once:

##### _“A Software is always work in progress...”_

So lets start with some problems which going to be our features of our final product. Here we’ll be discussing only about client server based issue, as where we work the most.

**Client Demo** – Problem comes when we have to demo our app to the client, what if server is down in that particular time. How we going to present our app? We’ll place dummy data here and there and then we’ll put so many if conditional blocks in our code to read from those dummy data instead of hitting web services. Right? We are not going to do that from now on.

**Slow Network Connectivity** – How do we test the slow network connectivity? Some people use our app with 3G/LTE network, some don’t. Some use it with WIFI or lets say 2G network. We always have to make sure that our application should work in every possible condition. It should show proper message, irrespective of network reachability.

**Mandatory Fields Check** – Generally when we write client server application we deal with so many GET/POST http header fields or post body fields. In which some are mandatory and some fields are non mandatory. For mandatory fields every time we have test it with server and wait for the response. What if the server has confirmed some mandatory fields and they have implemented it? Or our application might get stuck if we do not implement a mandatory field and server respond to it on production.

**Large Image / Video Upload** – How do we make sure that your application is sending image/video data correctly.

**Data Formatting and Validation** – GET/POST body data validation and formatting, JSON/XML validation or Email/DOB validation checks.

**Random Error Probability** – How our application behaves when server sends some error randomly. Its pretty interesting, the thought about Error Probability is like receiving errors from server randomly and seeing how our application is responding to it.

**UI Refreshing** – This is what we should always take care of, because this is what the end user deals with, here is what the experience they get.

**Session Setup** – If you want to check, when the server starts and ends the session.

## **Proxy Server**

And here comes the Proxy Server _(Local Host/127.0.0.1)._ Its HTTP, because it is one of the simpler protocols to implement for communication between computers. Since there is no APIs for **Stubbed** data downloading or data synchronisation; embedding a Proxy server in your application, is one of the best ways to transfer data from our local host to our iOS / Mac OS X applications. It’s an asynchronous networking using GCD and standard sockets programming between our local host and iOS applications. Here, I'll show you how to write your own simple but extensible HTTP server for iOS applications. The server classes will also work on Mac OS X.

### **How to configure**

Its pretty simple to configure HTTP Server. You’ll see those couple of lines of code to do the same. We only have to start a socket and attach a listener to it. It includes just four steps, that’s it. It is just a tiny HTTP server but the approach will allow you to quickly integrate HTTP communications into any application.

- Start Server - Open a Socket
- Receive Incoming Connections
- Response Handling
- Stop Server - Close Socket

### **Framework Needed**

Just three frameworks needed to start with and those are:

#import <sys/socket.h> 
//For #defines like AF\_INET protocol family.

#import <netinet/in.h> 
//Socket address structure and others defined parameter values.

#import <CFNetwork/CFNetwork.h> 
//Core Network APIs _(need to add into project)._

## **Opening a Socket**

Most server communications, HTTP included, begin by creating a socket for listening. Sockets in Cocoa can be created and configured entirely using the CoreFoundation _CFSocket_ API. It easier — But we still have a large block of boilerplate code to throw down just to open a socket.

### **Create Socket object:**

CFSocketRef socket = CFSocketCreate(kCFAllocatorDefault, PF\_INET, SOCK\_STREAM, IPPROTO\_TCP, 0, NULL, NULL);

_CFSocketCreate_ creates a _CFSocket_ object of a specified protocol and type. Here we pass the parameters of protocol family, stream socket and TCP protocol. _kCFAllocatorDefault_ is used for current default allocator.

### **Get Native Socket:**

int reuse = true;
int fileDescriptor = CFSocketGetNative(socket);
setsockopt(fileDescriptor, SOL\_SOCKET, SO\_REUSEADDR, (void \*)&reuse, sizeof(int));

_CFSocketGetNative_ returns the native socket associated with a _CFSocket_ object. If _CFSocket_ object has been invalidated, it returns -1 (_INVALID\_SOCKET)_.

### **Bind Socket to address:**

struct sockaddr\_in address;
memset(&address, 0, sizeof(address));
address.sin\_len = sizeof(address);
address.sin\_family = AF\_INET;
address.sin\_addr.s\_addr = htonl(INADDR\_ANY);
NSInteger portNumber = 8080;
address.sin\_port = htons(portNumber);
CFDataRef addressData = CFDataCreate(NULL, (const UInt8 \*)&address, sizeof(address));
CFSocketSetAddress(socket, addressData);

_sockaddr\_in_ is a C structure for socket address parameters like socket address, port number and etc. We create a _CFData_ object containing a _struct_ _sockaddr_ appropriate for the protocol family of socket object (_struct sockaddr\_in_ or _struct sockaddr\_in6,_ for example). This data object is used only for the duration of the function call.

_CFSocketSetAddress_ binds a local address to a _CFSocket_ object and configures it for listening. This function binds the socket by calling bind, and if the socket supports it, configures the socket for listening by calling listen with a backlog of 256. Once the socket object is bound to address, depending on the socket’s protocol, other processes and computers can connect to socket object. It returns an error code indicating success or failure.

## **Receive Incoming Notification**

So, we are done with opening a socket to listen for TCP connections on the port specified by _HTTP\_SERVER\_PORT_ (which was 8080). After the socket is setup, Cocoa handles a little more of the work so things get easier. Cocoa does a pretty good job for us by invoking an incoming connection notification, we only have to register a listener to it by constructing a file handler from native socket object. We can receive each incoming connection by constructing an _NSFileHandle_ from the fileDescriptor above and listening for connection notifications.

### **Constructing File Handler**

NSFileHandle \*listeningHandle = \[\[NSFileHandle alloc\] initWithFileDescriptor:fileDescriptor closeOnDealloc:YES\];
\[listeningHandle acceptConnectionInBackgroundAndNotify\];

_initWithFileDescriptor_ initialises and returns a file handle object associated with the specified POSIX file descriptor and deallocation policy. If flag is NO, the file descriptor you pass in to this method is not owned by the file handle object. In such a case, you are responsible for closing the file descriptor at some point after disposing of the file handle object. If you want the file handle object to close the descriptor for you automatically, pass YES for the flag parameter.

### **Attach Connection Listener**

\[\[NSNotificationCenter defaultCenter\] addObserver:self selector:@selector(receiveIncomingConnectionNotification:) name:NSFileHandleConnectionAcceptedNotification object:nil\];

### **Receive File Handler**

NSDictionary \*userInfo = \[notification userInfo\];
NSFileHandle \*incomingFileHandle = \[userInfo objectForKey:NSFileHandleNotificationFileHandleItem\];

_NSFileHandleNotificationFileHandleItem_ is a key in the user info dictionary in a _NSFileHandleConnectionAcceptedNotification_ notification. The corresponding value is the _NSFileHandle_ object representing the “near” end of a socket connection.

### **Attach Incoming Data Listener**

\[\[NSNotificationCenter defaultCenter\] addObserver:self selector:@selector(receiveIncomingDataNotification:) name:NSFileHandleDataAvailableNotification object:incomingFileHandle\]; 
\[incomingFileHandle waitForDataInBackgroundAndNotify\];

Then we attach another listener with this DataFileHandler for incoming data. _NSFileHandleDataAvailableNotification,_ this notification is posted when the file handle determines that data is currently available for reading in a file or at a communications channel. The notification object is the NSFileHandle object that sent the notification. This notification does not contain a userInfo dictionary instead it contains availableData for that connection.

## **Two file handlers**

\[caption id="attachment\_143" align="aligncenter" width="646"\][![Two file handlers on Local HTTP Server](https://izeeshan.files.wordpress.com/2014/08/3.png?w=646)](https://izeeshan.files.wordpress.com/2014/08/3.png) Two file handlers on Local HTTP Server\[/caption\]

When _receiveIncomingConnectionNotification:_ function is invoked, each new incoming connection will get its own NSFileHandle. If you're keeping track, that was:

- 1 file handle (_listeningHandle_) _**manually created**_ from the socket _fileDesriptor_ to listen on the socket for new connections, a **Connection Listening** file handler.
- 1 file handle _**automatically created**_ for _each_ new connection received through listeningHandle. We'll continue to listen to these new handles (the keys in the incomingRequests dictionary) to record the data for each connection, a **Data Listening** file handler for listing to available data on stream.

## **Receive Incoming Data**

### **Handle Incoming Data**

NSFileHandle \*incomingFileHandle = \[notification object\];
NSData \*data = \[incomingFileHandle availableData\];

### **Create HTTP Message to store date**

CFHTTPMessageRef incomingRequest = CFHTTPMessageCreateEmpty(kCFAllocatorDefault, TRUE);
CFHTTPMessageAppendBytes(incomingRequest, \[data bytes\], \[data length\]);

So, we've received a new, automatically created file handle. Now we’ll create a _CFHTTPMessageRef_ to store the incoming data we receive over the file handle. We store these as the objects in _incomingRequests_ dictionary to allow easy access to the _CFHTTPMessageRef_ for each file handle. _CFHTTPMessageCreateEmpty_ creates and returns a new, empty _CFHTTPMessage_ object then we call _CFHTTPMessageAppendBytes_ to store an incoming serialised HTTP request message in the empty message object.

_CFHTTPMessageAppendBytes_ function appends the data specified by newBytes to the specified message object which was created by calling _CFHTTPMessageCreateEmpty_. The data is an incoming serialised HTTP request or response received from a client or a server. While appending the data, this function deserialises it, removes any HTTP-based formatting that the message may contain, and stores the message in the message object. You can then call _CFHTTPMessageCopyVersion_, _CFHTTPMessageCopyBody_, _CFHTTPMessageCopyHeaderFieldValue_, and _CFHTTPMessageCopyAllHeaderFields_ to get the message’s HTTP version, the message’s body, a specific header field, and all of the message’s headers, respectively.

If the message is a request, you can also call _CFHTTPMessageCopyRequestURL_ and _CFHTTPMessageCopyRequestMethod_ to get the message’s request URL and request method, respectively.

If the message is a response, you can also call _CFHTTPMessageGetResponseStatusCode_ and _CFHTTPMessageCopyResponseStatusLine_ to get the message’s status code and status line, respectively.

The _CFHTTPMessageRef_ is both storage and the parser for the incoming data. We can invoke _CFHTTPMessageIsHeaderComplete()_ every time we add data to check when the HTTP headers are complete and we can spawn a response handler. The response handler is spawned in the _receiveIncomingDataNotification_ method. The server stops listening to the file handle for the connection at this point but it doesn't close it, since the file handle is passed to the response handler so that the HTTP response can be sent back over the same file handle.

**Sample Incoming Data**

GET / HTTP/1.1
Host: 127.0.0.1:8080
Accept-Encoding: gzip, deflate
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,/\*;q=0.8
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10\_10) AppleWebKit/538.34.48 (KHTML, like Gecko) Version/8.0 Safari/538.35.8
Accept-Language: en-us
DNT: 1
Connection: keep-alive

## **Handling Response**

### **Parsing Request Data**

NSURL \*requestURL = NSMakeCollectable(CFHTTPMessageCopyRequestURL(ref));
NSString \*httpMethod = NSMakeCollectable(CFHTTPMessageCopyRequestMethod(ref));
NSDictionary \*httpHeaderFields = NSMakeCollectable(CFHTTPMessageCopyAllHeaderFields(ref));
NSData \*httpBody = NSMakeCollectable(CFHTTPMessageCopyBody(ref));

Now we can parse the HTTP request body, _NSMakeCollectable_ makes a newly allocated Core Foundation object an _NSObject_ which is eligible for collection. This function is a wrapper for _CFMakeCollectable_, but its return type is id—avoiding the need for casting when using Cocoa objects. This function may be useful when returning Core Foundation objects in code.

### **Create HTTP Response**

NSInteger responseCode = 200;
CFHTTPMessageRef response = CFHTTPMessageCreateResponse(kCFAllocatorDefault, responseCode, NULL, kCFHTTPVersion1\_1);

Begin sending a response over the fileHandle. Trivial cases can synchronously return a response but everything else should spawn a thread or otherwise asynchronously start returning the response data.

_CFHTTPMessageCreateResponse()_ creates and returns a _CFHTTPMessage_ object for an HTTP response. This function returns a _CFHTTPMessage_ object that you can use to build an HTTP response. Then call _CFHTTPMessageCopySerializedMessage()_ to make the message ready for transmission by serialising it.

### **Add Response header fields**

CFHTTPMessageSetHeaderFieldValue(response, (CFStringRef)@"Content-Type", (CFStringRef)@"text/plain");
CFHTTPMessageSetHeaderFieldValue(response, (CFStringRef)@"Connection", (CFStringRef)@"close");

We can call _CFHTTPMessageSetHeaderFieldValue()_ function to set the message’s headers, _CFHTTPMessageSetHeaderFieldValue()_ sets the value of a header field in an HTTP message, and calling _CFHTTPMessageSetBody()_ to set the message’s body.

### **Add Data Length in header fields**

NSString \*dataLength = \[NSString stringWithFormat:@”%d", fileData.length\];
CFHTTPMessageSetHeaderFieldValue(response, (CFStringRef)@"Content-Length", (CFStringRef)dataLength);

### **Serialise HTTP Response in Data**

CFDataRef headerData = CFHTTPMessageCopySerializedMessage(response);

_CFHTTPMessageCopySerializedMessage()_ serialises a _CFHTTPMessage_ object. This function returns a copy of a _CFHTTPMessage_ object in serialised format that is ready for transmission. It creates a self-contained copy of a _CFHTTPMessage_. This would be suitable for persistent storage or for transmitting over the network independently.

## **Sending Response**

### **Write HTTP Header Data and Response Data**

\[incomingFileHandle writeData:(NSData\*)headerData\];
\[incomingFleHandle writeData:fileData\];

### **Close file handler and Remove Listeners**

\[incomingFileHandle closeFile\];
\[\[NSNotificationCenter defaultCenter\] removeObserver:self name:NSFileHandleDataAvailableNotification object:incomingFileHandle\];

## **Stop Server**

### **Remove Connection Listener**

\[\[NSNotificationCenter defaultCenter\] removeObserver:self name:NSFileHandleConnectionAcceptedNotification object:nil\];

### **Close Socket**

\[listeningHandle closeFile\];
CFSocketInvalidate(socket);
CFRelease(socket);

_CFSocketInvalidate()_ invalidates a _CFSocket_ object, stopping it from sending or receiving any more messages.

You should always invalidate a socket object when you are through using it. Invalidating a _CFSocket_ object prevents the object from sending or receiving any more messages, but does not release the socket object itself.

If a run loop source was created for s, the run loop source is invalidated.

If a release callback was specified in _CFSocketContext_ object, this function calls it to release the object in the info field (which was provided when s was created).

By default, this call closes the underlying socket. If you have explicitly cleared the _kCFSocketCloseOnInvalidate_ flag by calling _CFSocketSetSocketFlags_, you must close the socket yourself after calling this function.

## **Configure In App**

### **Start Server**

func application(application: UIApplication!, didFinishLaunchingWithOptions launchOptions: NSDictionary!) -> Bool
func applicationWillEnterForeground(application: UIApplication!)

### **Stop Server**

func applicationDidEnterBackground(application: UIApplication!)

Normally there's no need to run our server on any specific port. Technologies like Bonjour allow clients to dynamically discover the server's port at runtime. However, for easy testing you may want force a certain port so you can just hit the refresh button.

There is no public (allowed in AppStore) method for iOS to run continuously in the background for our purposes (serving HTTP). So, we stop the server when the app is paused (if a users exits from the app or locks a device) and restart the server when the app is resumed (based on this document: _http://developer.apple.com/library/ios/#technotes/tn2277/\_index.html_ )

## **Solutions**

- Set up Core Data or SQLite for dynamic tables
- Add delay before Response Handling
- Configure Error Probability On Response
- Configure Session Setup
- Add Server States _(Idle / Running)_ 
- Add Configurations like Port no, etc…

Set up Core Data for any specific project because you cannot add dynamic tables to it. In this case you only have to populate the core data with dummy responses.

You can also build a layer of SQLite for dynamic tables. This will make your dummy server more scalable. Which can help others as well.

**Delay** – You can add delays ask users to set the delay property for proxy server. This will help you to test slow network connectivity.

**Error Probability** – Create a property which will count for all your requests. You can increase counter when some one hit your server and then respond with error response in specific counter value lets say for all even or all odd.

**Session setup** – you can anytime configure it.

**Server State** – Its good for end user and for server writer as well to keep the state of your server. Whether its in Idle or running state.

**Other Configurations** – Like Port no. which can define any specific port no. Which will prevent others to guess.

##### **References**

- http://www.cocoawithlove.com/2009/07/simple-extensible-http-server-in-cocoa.html
- https://github.com/robbiehanson/CocoaHTTPServer
- https://developer.apple.com/library/ios/technotes/tn2277/\_index.html

###### [Sample Project](https://github.com/zeeshankhan/ZKProxyServer "Sample Project")

##### You can also find the presentation on it, [Proxy Server PPT](https://speakerdeck.com/izeeshan/proxy-server "Proxy Server PPT")

##### Thanks for reading! :)
