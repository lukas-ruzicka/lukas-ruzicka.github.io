---
layout: post
title: Augmented Reality and its easier usage with iOS 13 and “Reality” tools? Not quite so...
description: Apple is without a doubt a few steps ahead of the competition in terms of augmented reality. This year at WWDC, it came up with more possibilities, thanks to which they got ahead of competition even more.
date: 2020-02-21 00:00:00 +0000
image: '/images/posts/ios13-reality-tools/preview.jpg'
image_caption: 'Photo from the [App Parade 33](https://appparade.cz)'
tags: [augmented reality]
---

ARKit has been extended to include features like [People occlusion](https://youtu.be/SgAnnwl2VB8) ("overlapping" of virtual objects when they are behind a human), [Body tracking](https://youtu.be/SgAnnwl2VB8) (creating a human skeleton and recording the skeleton's movements) or Collaborative sessions (simple AR scene sharing between multiple devices). And again, the whole AR development was made even more accessible thanks to a high-level framework **RealityKit** and also an *all-powerful* AR tool **Reality Composer**. These tools further simplified an already very simple implementation (regarding the complexity of the whole AR technology solution).

**RealityKit and Reality Composer**

RealityKit is the first framework for realistic image rendering from Apple, designed specifically for usage in augmented reality. It handles objects rendering and texturing in relation to the space into which they are placed (physical based rendering). It uses a very efficient *Metal* framework for that. Thanks to that the rendering is even less demanding on device performance and capacity. Object types have also been simplified compared to the previously used *SceneKit*, so that everything meets the conditions of augmented reality.

But the thing with which Apple made the development accessible the most is the **Reality Composer**. This app is available for both macOS and iOS (or iPadOS). It is easy to assemble an AR scene with it, including animations and interactions with objects, and everything can be prepared not only in virtual 3D space, but directly in the real environment. A scene anchor can be a horizontal / vertical surface, a face, a picture, or even a real object.

An exported .reality file can be run and viewed in augmented reality without the need for another application directly on the iPhone / iPad. Likewise, this file, or the entire project, can be inserted directly into Xcode, where you can just load it in the code (use the code that is automatically generated after the project is added) and insert it into the AR scene. Because the RC project can be located directly in the repository, there is a great opportunity to reflect the changes in the RC project directly in the application code. For example, if you want to move one of the objects, just open the RC project, move the object, and simply build the application without further steps.

> Any iPhone or iPad with iOS 13 (or iPadOS 13) and an A9 chip or higher (i.e. iPhone 6S / iPad 2017 and later) will do the trick.

### It all sounds too beautiful to be really that simple.

Given that augmented reality is still very fresh technology, we can expect, that not everything will be perfectly tuned. Even though Apple is a guarantee of quality in this field, it seems that with this year's news it got a little too much ahead of itself.

You can see this already on the frequent operating system updates, which still fix a considerable number of errors and flaws. These fixes addressed, among others, the mentioned augmented reality tools, where some solutions had several fundamental limitations and malfunctions. I would like to show you some of these.

## SYNETECH & augmented reality

We tried new tools in the AR zone at the [AppParade](https://appparade.cz/) competition, which we organise. We regularly try to bring people closer to and let them touch the latest trends and technologies, where AR undoubtedly has its place.

Augmented Reality is a technology that we are actively engaged in and develop its capabilities within our own product called Synevision. This time, as part of the [**AppParade 33**](https://appparade.cz/historie) held in November 2019, we decided to create a **concept called** **Museum of the Future**. It was about exhibiting only exhibits without any other info panels or signs and the visitor learned about them just in augmented reality. The central motive was a question: what iPhone gave to and took from the world?

<div class="gallery-box">
  <div class="gallery">
    <img src="/images/posts/ios13-reality-tools/appparade_33_arzone.jpg" loading="lazy" alt="App Parade 33 AR zone">
  </div>
  <em>Photo from the <a href="https://appparade.cz" target="_blank">App Parade 33</a></em>
</div>

With that what Apple promised at WWDC in June, **it was supposed to be a really simple project**. Apparently it was only about scanning the exhibit reference models (using the [demo application](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects), provided directly by Apple engineers), create text panels and just add everything to a scene in *Reality Composer*. Then insert this project into an app project, load and add everything directly to ARView when starting the AR scene. Everything else should be handled by *RealityKit*. This is one of the reasons why we expected development to take a minimal amount of time. However, we have encountered so many limitations that the development period has extended several times. Fortunately, we started well in advance 🙏🏼

## The most essential problems we encountered while implementing the solution:

In the end we managed to get the AR zone into a usable form and it met with success. I will describe all the problems that arose along with solutions. Perhaps this will save someone from grey or torn out hair 👴🏼

### Difficult reference model detection

As mentioned above, a scene can be created in the Reality Composer *(RC further on)* project. It relies on an anchor that is a real physical object. All that was needed was to add the reference model to the project, place information panels around it, put all this into Xcode and then add it to a scene at the start of the AR session. At the end, everything should be done with the following 3 lines of code:

{% highlight swift %}
let entity = try! Entity.loadScene()
arView.scene.anchors.append(entity)
arView.session.run(config, options: options)
{% endhighlight %}

Although the reference model confidently figurated in the RC project (it was detected correctly when previewing within an Reality Composer iOS app), no detection was successful after adding it to our application. After hours of trying everything possible and reading different forums, I found out that the output of Reality Composer is not ready to be detected as a reference model. All that I was left to do was to take care of the detection myself.

The detection can be started as follows:

{% highlight swift %}
// Loading reference objects from the assets
guard let referenceObjects = ARReferenceObject.referenceObjects(inGroupNamed: "AppParade33Exhibition", 
								bundle: nil) else { return }
config.detectionObjects = referenceObjects
// It's required to assign delegate to the session for "listening" of the scene development
arView.session.delegate = self
arView.session.run(config, options: options)
{% endhighlight %}

After successful detection (this can be captured through the `ARSession` delegate method) all that's needed to do is to to insert the RC output on the detected *Anchor*. Since it is no longer necessary to hold a reference anchor of a real object in the RC project, this anchor can be changed to a horizontal surface. The addition of the RC project's output to a scene looks like this (in a simplified form):

{% highlight swift %}
func session(_ session: ARSession, didAdd anchors: [ARAnchor]) {
	// `ARObjectAnchor` is anchor of the defined reference object
	guard let objectAnchor = (anchors.first(where: { $0 is ARObjectAnchor })) as? ARObjectAnchor else { return }

	let entity = try! Entity.loadScene()
	// Position of the added entity is defined by the following assinging of the `AnchoringComponent` (with identifier of the found anchor)
	entity.anchoring = AnchoringComponent(.anchor(identifier: objectAnchor.identifier))
	arView.scene.addAnchor(entity)
}
{% endhighlight %}

### Sometimes the RC project simply broke...

I haven't discovered the cause to this day, but many times the RC project just decided that it wouldn't be possible to retrieve it from the code. Subsequently, I was always forced to create a new project in which I took exactly the same steps and miraculously it was possible to reload the scene. This error occurred quite randomly.

"Fortunately", this problem did not have to bother us, because the RC project and its loading directly in the code could not be used in the end. One of the reasons was backward compatibility.

### Support for older iOS versions

Of course, we knew that everything we create using Reality tools would only be supported from iOS 13 on, and we expected that the *AR zone* tab will be visible to the most of Apple phone users and not to everybody. What we didn't expect, was that we would have to integrate everything that would normally be handled by `if #available (iOS 13.0, *)` or `@available (iOS 13.0, *)` annotations.

From the beginning, we have developed all the functionality in a separate library for the possibility of reuse. To get it into a working form, we didn't bother ourselves with setting the minimal version lower than 13. Everything just fell apart in the moment when we tried to integrate the library into AppParade app.

The first problem occurred during the build-time. The issue wasn't anything else than the automatically generated code for interaction with the RC project. The fact that the library itself wouldn't be able to provide resources (such as RC projects) was clear immediately when we realised that the generated files were somehow "hidden somewhere" and did not show up in the project tree. However, Apple developers haven't thought of one essential thing - that someone might want to use the output of RC projects only as a complementary part of an application and therefore they did not wrap the generated code into blocks specifying the necessary iOS version (`available (iOS 13.0, *)`) 🤦‍ .

Given that the generated code also included the structure of notifications that objects sent, for example, after interaction or animation, the generated code was more than necessary. Fortunately, after analysis, we found that the code does nothing but loads a *.reality* file that can be exported from the RC project and defines their interactions using the keys defined in the project. As time began to push us, there was nothing else to do but to generate the code in a project with a minimum version of iOS 13.0, copy it and tag classes with `@available (iOS 13.0, *)`. Afterwards, just generate *.reality* files, insert them into the project and edit the path to them in the originally generated code.

The original glorious idea of ​​keeping RC projects in a repository and projecting the live changes directly into the code has, of course, completely fallen apart... The only missing line in the generation template totally broke the originally beautiful idea...

<div class="gallery-box">
  <div class="gallery">
    <img src="/images/posts/ios13-reality-tools/desperate_jim_carrey.gif" loading="lazy" alt="Desperate Jim Carrey meme">
  </div>
</div>

After solving a successful compilation, there was another problem. To be fair, this problem is not directly related to Reality tools, but generally to the use of native frameworks with iOS versions higher than the application's *deployment target*. I would just like to mention this for anyone who might get stuck on the same problem.

If you want to use such framework, it is not enough to just to wrap its functionality in `if #available` blocks or entire classes, within which it is used, with`@available` tag. Under these conditions, the application with lower iOS version crashes immediately. It is also necessary to define the framework import as *weak*. Just add `-weak_framework "_FrameworkName_”` to **Other Linker Flags** in **Build Settings**, then iOS will ignore any framework imports that are not available to it.

<div class="gallery-box">
  <div class="gallery">
    <img src="/images/posts/ios13-reality-tools/weak_framework.png" loading="lazy" alt="Weak framework">
  </div>
</div>

If you are using the library as a *cocoapod*, you must also generate this *flag* into *Pod project*. To do this, add the following block into the *Podfile*:

{% highlight ruby %}
post_install do |installer_representation|
	installer_representation.pods_project.targets.each do |target|
		if target.name == '_The pod name_'
			target.build_configurations.each do |config|
				config.build_settings['OTHER_LDFLAGS'] = ["-weak_framework", "\"_The native framework name_\""] 
			end
		end
	end
end
{% endhighlight %}

## Rotating the object to face the camera? Feature that suits perfectly for our project! Almost...

When we came across the “look at camera” function in Reality Composer that could be assigned to an object, we were very happy about it. It was exactly the feature we expected from virtual information panels (so that the visitor was not dependent on the point of view while looking at the exhibit). Unfortunately, it wasn't again that simple.

In *Reality Composer,* the objects' actions can always be defined **only for a predetermined time and cannot be canceled in the course of it**. In general, for example, canceling an animation based on user interaction would look great in otherwise simple action creation. However, the fact that the *look at camera* also has the above-described features completely compromises this action.

If I just want the object to rotate towards the camera for the entire duration of the scene, I have no choice but to set a fixed rotation time (up to 5 minutes) and then send a notification that alerts me to start the action again. However, any other animation of the object is completely buried by this. Previously, we designed double-sided information panels to have the right virtual “feeling” and to make them move in space themselves (by tapping them which caused them to turn around). But if the rotation towards camera was on, there was no animation, of course, because **rotation towards the camera overrode any movement**.

The only way to ensure the functionality of both actions at the moment was to reduce the length of rotation towards the camera and putting a lot of work into notification handling. It was then enough to check whether the user tapped on the panel during the last rotation when deciding if the rotation towards the camera should proceed. If so, the rotation animation started and after the rotation the look at camera started again. Everything seemed to work beautifully, but only until the second set of panels was placed into the scene. Even the most powerful devices suddenly had a problem to manage the demanding process (instead of the camera feed we saw just blurry images, which refreshed after nice 3 seconds). We have found that the notification handling is simply incredibly unoptimized (or not intended for frequent interactions).

Eventually, we figured out a solution on how both animations could work at the same time. All that was needed was to assign an action to the objects, export the *.reality* file, and insert it into a new project where an additional action was added. If I mentioned earlier that we lost the opportunity to reflect the changes live into the application project, this has ruined it totally. However, the deadline was inexorably approaching, so we decided not to use the look at camera just to be safe.

<div class="gallery-box">
  <div class="gallery">
    <img src="/images/posts/ios13-reality-tools/appparade_33_arzone_summary.gif" loading="lazy" alt="App Parade 33 AR zone">
  </div>
  <em>Photo from the <a href="https://appparade.cz" target="_blank">App Parade 33</a></em>
</div>

## Conclusion

Apple's direction in the augmented reality field is definitely right, bringing this world closer to everyone - even to the people with no programming experience. It not only keeps its competitive edge but still increases it.

Unfortunately, this year Apple probably bitten off too much. This is not just about the augmented reality, as evidenced by the fact that in the first weeks since the release of the new iOS, updates and bug fixes were coming out in days. Hopefully, Apple will learn from these slips and provide guaranteed quality services again, even at the cost of minor delay.

There has been some rumours that Apple should finally introduce augmented reality glasses this year, but as we could see on the presented case, there is still work to be done. We'll see then what other features will be presented at this year's WWDC.

However, regardless of the current flaws, another groundbreaking technology with the potential to change our perception of the world around is already knocking confidently on the door 👀.

***

Original article can be foung on [SYNETECH blog](https://synetech.cz/en/blog/dev-augmented-reality-and-its-easier-usage-with-iOS-13).