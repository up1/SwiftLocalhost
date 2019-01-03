# SwiftLocalhost
Simple framework that creates a mock localhost server primarily used for XCUITest.
### Installation

##### [CocoaPods](http://cocoapods.org)

SwiftLocalhost is available through CocoaPods. To install it, simply add the following line to your Podfile:
```ruby
pod 'SwiftLocalhost'
```

## How to use - Follow these 4 steps to setup
### 1. [Localhost] - pod install `SwiftLocalhost` to your XCUITest target.

There are 2 ways to create an instance of a Localhost.

Using a specific port number
```swift
LocalhostServer(portNumber: 9001)
```

Getting an instance with a random unused port number assigned to it. This is important if you want to execute your tests parallelly 
```swift
LocalhostServer.initializeUsingRandomPortNumber()
```

An example of how a XCTestCase class looks like
```swift
import SwiftLocalhost

class MovieListTest: XCTestCase {
    
    var localhostServer: LocalhostServer!
    
    override func setUp() {
        continueAfterFailure = false
        self.localhostServer = LocalhostServer.initializeUsingRandomPortNumber()
        self.localhostServer.startListening()
    }
    
    override func tearDown() {
        self.localhostServer.stopListening()
    }
}
```

### 2. [API] - Modify App API Requests with http://localhost:xxxx (where xxxx is port number)

If you are using a specific port for your localhost, you can simply change the domain to http://localhost:xxxx.
```swift
class NetworkOperations {

    //private let baseUrl: String = "https://api.themoviedb.org/3/movie"
    
    private let baseUrl: String = "http://localhost:9001/3/movie"
}
```

If you are using random port numbers in your tests, then you will need to pass the port information into your app as launch arguments.

```swift
//In Test Runner, pass the port information using launchArguments
let app = XCUIApplication()
app.launchArguments = ["port:\(self.localhostServer.portNumber)"]
app.launch()

//In Application - Read the ProcessInfo Arguments
ProcessInfo..processInfo.arguments
```

If you need to redirect 3rd party libraries (eg. Firebase, Google Analytics) to the localhost server, you can use [NetworkInterceptor](https://github.com/depoon/NetworkInterceptor) pod created by Kenneth Poon.

### 3. [Info.plist] - Modify App Info.plist
Since we will be using `http` protocol to communicate with our localhost server, we will need to add an exception domain for `localhost` in your Info.plist file.

![picture alt](./Resources/Info-plist-add-exception-domain.png)
