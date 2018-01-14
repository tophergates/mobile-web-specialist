# 03.05: THE SERVICE WORKER LIFECYCLE
We have already experienced an oddity of the Service Worker lifecycle. When we first created the Service Worker, it took two page refreshes to see the result. In addition, when we changed the Service Worker the browser didn't seem to pick up that change. The lifecycle of the Service Worker is one of the most complex parts; once you get this bit the rest is easy!

## THE LIFECYCLE
Just to recap, we had a window with the Wittr app open already, then we added new code to register a Service Worker, then we hit refresh in the browser.

Hitting refresh the first time spawned a new window client. Then the request went off to the network. We received a response back, and the old window client went away. It might not seem like there's an overlap between the old page and the new page when you hit refresh, but there is! For example, if the response came back indicating that the browser should save the resource to disk via a download dialogue then the old window would've stayed around. But in this case, the response was a page so we got rid of the old one.

From our page, requests went out for our CSS, images, but also our javascript which registered the Service Worker. We didn't see requests log from this page, because the Service Worker only takes control of pages when they're loaded; and this page was loaded before the Service Worker existed. That means, any additional requests this page makes will bypass the Service Worker.

But then... we refreshed the page again. Creating a new window client. And because our Service Worker was up and running, it took control of it. Therefore, the request went to the Service Worker (as did all of the sub-resources).

**So that explains why it took two refreshes to see logged requests, but what about when we changed the thing we were logging yet still saw requests being logged from the old code?**

If a page loads via a Service Worker, it will check for an update to the Service Worker in the background. If it finds it has changed, the changed Service Worker becomes the next version. But the next version doesn't take control yet, it waits. It won't take over until all pages using the current version are gone. This ensures there is only one version of your site running at a given time.

Unfortunately, a refresh doesn't let the new version of the Service Worker take over. This is due to the overlap between window clients, which means there isn't actually a moment when the current active Service Worker is not in use. **For that to happen, the page needs to close or navigate to a page that isn't controlled by the Service Worker.** When it does that, the new Service Worker takes over and future page loads will go to the new one.

The update process for Service Workers may sound complicated, but if you think about it this is actually the same process that normal software uses. Take Chrome for example: When there is an update to the browser, it will be downloaded in the background, but will not take affect until the browser is closed and reopened again.

## CACHE TIME
When the browser refetches the Service Worker, looking for updates, it will go through the browser cache - as pretty much all requests do. Because of this, it is recommended keeping the cache time on your Service Worker short. In fact, a cache time of 0 is recommended.

As a safety precaution, if you set Service Worker's script to cache for more than one day the browser will ignore that and set the cache to 24 hours.

That doesn't mean your Service Worker will stop working after 24 hours, it just means that update checks will bypass the browser cache if the Service Worker it has is over a day old.

- - -

Next: [Enabling Service Worker Dev Tools](./06-dev-tools.md)