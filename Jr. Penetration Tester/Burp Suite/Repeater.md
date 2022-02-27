# Repeater

### Task 2 What is Repeater?
The repeater allows us to craft and/or relay intercepted requests to a target at will. We can take the request, edit it and send the same request repeatedly as many times as we want. Alternatively, we could craft requests by han, much as we would from something like `curl` to build and send requests.

Interface is broken into six main sections:
1. Repeater requests in top left of the tab.
2. Current request
3. Request and response view
4. Set of options of displaying request/response view
5. Inspector to the right which allows you to break requests apart to analyze and edit them in a slightly more intuitive way than with the raw editor.
6. Above inspector is the target which is the IP address or domain of the target this request is going to.

### Task 3 Basic Usage
With a request captured in the proxy, we can send to repeater either by right-clicking on the request and choosing "Send to Repeater" or by pressing `Ctrl + R`

### Task 4 Views
Four display options here:
1. **Pretty**: Default option, it takes the raw response and attempts to beautify it slightly, making it easier to read.
2. **Raw**: The pure, un-beautified response from the server.
3. **Hex**: Gives the byte view of the respone.
4. **Render**: renders the page as it would appear in your browser.

### Task 5 Inspector
It is a superb way to get a prettified breakdown of the requests and responses, as well as for experimenting to see how changes made using the higher-level Inspector equivalent raw versions.

Allows you to edit the request

- Query Parameters 
- Body Parameters
- Request Cookies
- Request Headers
- Response Headers

### Task 6 Example

