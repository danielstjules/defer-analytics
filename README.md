# defer-analytics

Easily defer loading and firing events for Google Analytic's new web tracking
code:
[analytics.js](https://developers.google.com/analytics/devguides/collection/analyticsjs/).
Achieves some of the flexibility lost in switching from the soon to be deprecated
[ga.js](https://developers.google.com/analytics/devguides/collection/gajs/).
Useful in cleaning up your data and reducing noise by delaying or blocking
events for suspicious or illegitimate traffic.

* [Overview](#overview)
* [Installation](#installation)
* [Example](#example)
* [Notice](#notice)

## Overview

With the old ga.js library, one could push as many events as possible onto an
array, to eventually be handled by Google Analytics once loaded.

``` javascript
var _gaq = _gaq || [];
_gaq.push(['_setAccount', 'UA-XXXXX-X']);
_gaq.push(['_trackPageview']);
_gaq.push(['_trackEvent', 'important_event', 'occurred']);
```

Furthermore, you could previously delay loading google-analytics.com/ga.js
as much as you'd like. All would still work, for example, while wrapping it
in a call to setTimeout.

``` javascript
_gaq.push(['_trackEvent', 'important_event1', 'details']);
_gaq.push(['_trackEvent', 'important_event2', 'details']);

// Load GA after 5 seconds
setTimeout(function() {
  var ga = document.createElement('script'); ga.type = 'text/javascript'; ga.async = true;
  ga.src = ('https:' == document.location.protocol ? 'https://ssl' : 'http://www') + '.google-analytics.com/ga.js';
  var s = document.getElementsByTagName('script')[0]; s.parentNode.insertBefore(ga, s);
}, 5000);
```

In the example above, all events pushed onto _gaq would be processed
asynchronously at least 5 seconds after page load, plus the latency of loading
ga.js. This, however, is not currently possible with the new analytics.js library.

``` javascript
// Try to load analytics.js after 5 seconds
setTimeout(function() {
  function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');
}, 5000);

ga('create', 'UA-XXXX-Y', 'auto');
ga('send', 'pageview');
// ReferenceError: ga is not defined
```

But it is with defer-analytics:

``` javascript
// initAnalytics loads analytics.js after 5 seconds
// All calls to ga() succeed and are processed once loaded
setTimeout(initAnalytics, 5000);

ga('create', 'UA-XXXX-Y', 'auto');
ga('send', 'pageview');
```

## Installation

Simply substitute your existing analytics.js snippet:

``` javascript
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','//www.google-analytics.com/analytics.js','ga');

ga('create', 'UA-XXXX-Y', 'auto');
ga('send', 'pageview');
```

With:

``` javascript
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();
i.initAnalytics=function(){a=s.createElement(o),m=s.getElementsByTagName(o)[0];
a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
}})(window,document,'script','//www.google-analytics.com/analytics.js','ga');

ga('create', 'UA-XXXX-Y', 'auto');
ga('send', 'pageview');

// Initialize GA when you see fit
initAnalytics();
```

Done.

## Example use

You run an e-commerce website, a SaaS company, or a personal blog
for your pet corgi. Like any good webmaster/developer/digital-marketer, you
embed the analytics script for tracking visitor behavior, ad spending, and
conversion!

One day you find your reports polluted with a segment of daily visitors with
100% bounce rate. Or who have a session duration of 0.001 seconds. It's a small
botnet! And while your site can handle the increase in traffic, your conversion
rates plummet. You lose insight into your visitor behavior. Your ad retargeting
budget is wasted on infected machines. And the GA filters aren't sufficiently
flexible to remote the noise. What can you do?

You could delay or avoid loading Google Analytics for those visitors when
you detect them. So now you have a bunch of custom `ga('send', ...)` calls
spread throughout your codebase that need to be wrapped with special logic.
Or you can just use defer-analytics! Now you can just do something like:

``` javascript
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();
i.initAnalytics=function(){a=s.createElement(o),m=s.getElementsByTagName(o)[0];
a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
}})(window,document,'script','//www.google-analytics.com/analytics.js','ga');

ga('create', 'UA-XXXX-Y', 'auto');
ga('send', 'pageview');

if (!illegitimateTraffic) {
  initAnalytics();
}
```

## Notice

Use at your own risk! The code has been modified from the original snippet
provided and distributed by Google under the Apache License.

```
Copyright 2015, Google

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
