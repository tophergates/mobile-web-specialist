# 02.03: THE BENEFITS OF OFFLINE FIRST
What can we deal with network failures and delays?

We could attempt a network fetch, and if that fails get fallback content from a cache. However, we have to wait for the network to fail first. If the connection is slow, users are still waiting for content.

The gold-standard is offline first. It means getting as many things on the screen as possible using as many resources as we can that are already on the users device. We may still go to the network, but we aren't going to wait for it.

When we get fresh data from the network, we can update what the user sees and cache that data on the users device.

This is the offline-first approach. This means the user is happy online, offline, and even with lie-fi. The less the users has to care about connectivity, the better!

- - -

Next: [Quiz: What Can Slow Us Down?](./04-quiz-what-slows-us-down.md)