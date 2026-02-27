## Content Delivery > CDN > Overview

The CDN service allows you to store the original content to be distributed on the origin server, so that the content can be distributed to cache servers around the world. End users can get files from the nearest cache server at high speed.

In addition, if the service provider uses the CDN service, since most of the traffic is handled by the cache server, the service failure caused by the congestion of the origin server can be prevented in advance and the availability of the origin server can be increased.

When delivering content to end users, it is possible to provide much higher quality service by using a dedicated line rather than a general Internet network, thereby increasing service reliability.

## Main Features

- Control user access with cache settings.<br/>
  You can use referrer information to control access to user content. You can also enter the information in the form of a regular expression and control multiple referrers at the same time.
- Set the cache expiration time as you like.
- Cache purging<br/>
  When the original content changes, the cache is updated with the new content after the previously specified cache expiration time. However, the 'cache purging' feature lets you quickly update an existing cache with new content.
- Global network<br/>
We provide high-speed service using worldwide cache servers.
- HTTP/2 protocol support<br/>
  The HTTP/2 protocol is supported by default for any CDN service you create. You can use HTTP/1, HTTP/1.1, and HTTP/2 protocols without any additional setting.