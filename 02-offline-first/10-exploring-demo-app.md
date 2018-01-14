# 02.10: EXPLORING THE DEMO APPS CODE
When you navigate to Wittr, the browser makes a request for some HTML. Like all web requests, this goes via the browsers HTTP cache, if there is no match there it continues on to the internet. Then, hopefully the response makes it way back to the browser. Once the response arrives we get our initial content.

The JavaScript for the Wittr application opens a `WebSocket`. A `WebSocket` is a persistent connection that lets the server continually stream new posts as they arrive.

The main work happens in: `public/js/main/views/IndexController.js`

The constructor is run for every page load. While the server is running, any changes made to these files will be rebuilt by the build script if the server is running.

There is another server running at http://localhost:8889/

This is a basic control over connectivity to the server. Currently, the application only works well when the user is online, but over the course of this course we will fix non-perfect cases.

- - -

Next: [Quiz: Changing Connection Types](./11-quiz-changing-connection-types.md)