# Web Animations Utilities

This repository contains utility libraries for Web Animations, which is a new JavaScript API for driving animated content on the web.

For more information, see the [specification](https://w3c.github.io/web-animations/), [browser support](http://caniuse.com/#feat=web-animation), and [polyfill][polyfill].

## Usage

### Timeline

The [timeline library](timeline.js) provides a manager of many `Animation` player instances, and is useful for scheduling and scrubbing a collection of animations and related callbacks.

It is supported natively on Chrome 39+, and requires the [web-animations][js] polyfill on other, modern browsers.

```js
var timeline = new AnimationUtilTimeline();

var element = ...;
var steps = [{transform: 'translate(0)'}, {transform: 'translate(1000px)'}];

timeline.schedule(250, element, steps, 1500);
timeline.call(250 + 750, function() {
  console.info('half-way there!');
  timeline.playbackRate *= 2;

  // schedule a further animation, after the timeline has started.
  timeline.schedule(1500, otherElement, differentSteps, 1000);
});
```

### Props

The [props library](props.js) provides helpers to apply inline CSS properties after an animation has completed. It builds handlers to add to the `finish` event on an `Animation` that apply the final keyframe values seen.

It is supported natively on Chrome 39+, and requires the [web-animations][js] polyfill on other, modern browsers.

```js
var element = ...;
var steps = [{transform: 'translate(0)'}, {transform: 'translate(1000px)'}];
var anim = element.animate(steps, 1000);

anim.addEventListener('finish', AnimationUtilApply(steps, element));
````

It also supports the advanced polyfill features in [web-animations-next][js-next], such as `KeyframeEffect`, `GroupEffect` and `SequenceEffect`.

```js
var effect = new KeyframeEffect(target, steps);
var group = new GroupEffect([effect, ...]);
var anim = document.timeline.play(group);
anim.addEventListener('finish', AnimationUtilApply(effect));

// Or the AnimationUtilApplyHandler is supported where Animation exposes
// its effect property correctly: the handler below is equivalent to passing
// `anim.effect` to the base helper.
anim.addEventListener('finish', AnimationUtilApplyHandler);
````

### Externs

Externs can be used inside the [Closure Compiler](https://developers.google.com/closure/compiler) to hint at functions provided outside a project's code base, such as third-party libraries or for upcoming APIs such as Web Animations.
For more information, see [Advanced Compilation and Externs](https://developers.google.com/closure/compiler/docs/api-tutorial3).

The utility library provides an externs file that may be used in Closure.

```bash
java -jar compiler.jar --compilation_level ADVANCED_OPTIMIZATIONS \
  --js yourcode.js --externs web-animaitons-utils/externs.js
```

[polyfill]: https://github.com/web-animations/web-animations-js
[js]: https://github.com/web-animations/web-animations-js#web-animationsminjs
[js-next]: https://github.com/web-animations/web-animations-js#web-animations-nextminjs
