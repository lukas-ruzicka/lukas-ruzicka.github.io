---
title: tado°
description: Mobile app for managing smart thermostats and other heating and cooling options in the home.
category: iOS app development
from: 2021-06-01 00:00:00 +0000
to: 2022-10-31 00:00:00 +0000
client: SYNETECH s.r.o.
role: iOS engineer
image: '/images/works/tado/lifestyle.jpg'
image_caption: 'tado° lifestyle photo'
appstore_link: 'https://apps.apple.com/app/tado/id574418486'
---

I worked at tado° for almost a year and a half as an external engineer. I was part of the Auto-Assist team that handles the paid features in the app - in particular the Energy IQ feature that provides information about the user's consumption in a readable form.

<div class="gallery-box">
  <div class="gallery">
    <img src="/images/works/tado/thermostat.jpg" loading="lazy" alt="Smart Thermostat">
    <img src="/images/works/tado/energyiq.jpg" loading="lazy" alt="Energy IQ">
  </div>
  <em>Photos provided by <a href="https://www.tado.com/">tado°</a></em>
</div>

> tado° sells custom hardware for controlling heating and cooling in homes. Everything is managed via a mobile app.

From a tech perspective, this project was quite challenging because the app has several years on its back, so there was a lot of legacy code and a mixture of architectures and different patterns. We started introducing SwiftUI, but it required a lot of integrations to make it work with the UIKit remains of the app. We also introduced Swift concurrency there and refactored most of the networking implementation, so it became much more readable.