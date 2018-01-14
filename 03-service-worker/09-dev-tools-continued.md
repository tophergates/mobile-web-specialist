# 03.09: SERVICE WORKER DEV TOOLS CONTINUED
Having to close the tab or navigate away from the page in order to get the Service Worker to update to the new version is a bit of a pain during development, but thankfully there are ways to make it easier.

Rather than closing the tab or navigating to another origin, reload the page while holding `SHIFT`. This loads the page, but bypasses the Service Worker. This is handy for two reasons:

  1. It's a quick way to test changes that are unrelated to the Service Worker, such as minor CSS changes.
  
  2. Because the tab is no longer controlled by the Service Worker, it lets the waiting Service Worker take over.

If you refresh normally now, the request will go through the new Service Worker.

This way of bypassing the Service Worker and then refreshing is easier than navigating away from the origin or closing the tab, but Chrome's developer tools offer an even easier way:

In the `Application` tab of Chrome developer tools, there is an option called `Force update on page load`. This changes the Service Worker lifecycle to be developer friendly. In this mode, when you hit refresh, rather than refreshing the page it fetches a Service Worker and treats it as a new version whether it has changed or not and lets it become active immediately. With this option active, you do not have to hold `SHIFT` and refresh or navigate away from the page.

- - -

Next: [Hijacking Requests](./10-hijacking-requests.md)