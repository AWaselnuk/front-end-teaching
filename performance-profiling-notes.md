# Front end performance profiling

## Question everything and everyone

* Make sure everyone actually knows what they want when they are adding features or dependencies. Every byte counts.
* Question history itself. Things that made sense in the past might no longer make sense. Why do we actually have this library here? (Moment.js is a good example)
* Question dev environment, 3rd party libs, designers, PMs, fellow devs, your own assumptions.
* We are the last line for perf in most cases.

## Dev tools

* Use profiling tools in all browsers.
* Sometimes chrome devtools is wrong so checkout Firefox too.
* The Firefox waterfall is very readable.
* Safari has a 'rendering frames' section
  * You have roughly 30ms to do any important work (because our goal is 60 FPS). Look for frames that are above this and dig in.
  * CSS design can affect the layout work needed. You can't actually totally prevent reflows but you can minimize them. An example is when the admin team hide the font fingerprinting in an absolutely positioned div. (font fingerprinting: printing every single system font into the DOM and measuring it)

## Look for CPU thrashing

* Activity monitor on OSX reveals CPU usage. In home, skeleton animations are eating a ton of CPU resources because we are animating background position.

## Reflows

* Reflows cause jank.
* Reflows are necessary for cool stuff to happen so you can't just delete them all.
* Aim for minimum number of reflows. Too many reflows lead to something called [layout thrashing](https://developers.google.com/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing?hl=en).

## Measuring performance

* Go to incognito to disable extensions and noise
* Disable caching
* Network throttling to mobile first at Regular 3G.
* Refresh with command-shift-r because it blows away all cache.
* HOT TIP: Scroll the dev tools waterfall with click and drag
* Avoid having screenshots turned on because this adds overhead
* Avoid having multiple dev consoles open at once because this leads to strange bugs and inconsistencies

## Find solutions to perf problems

* Find low hanging fruit quickly with PageSpeed Insights. Similar features in the Audit tab in devtools.
* HTTP2 for parallelized image requests.
* Tools for finding unused CSS might be great but how do we architect multiple bundles in Rails?
