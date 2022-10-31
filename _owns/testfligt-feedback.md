---
title: TestFlight feedback
description: Tool that is designed to ease management of user feedback received through TestFlight.
category: Open-source tool
from: 2022-09-22 00:00:00 +0000
image: '/images/owns/testflight-feedback/tf-logo.jpg'
image_caption: 'TestFlight logo from [Apple developer website](https://developer.apple.com/testflight/)'
github_link: 'https://github.com/lukas-ruzicka/testflight-feedback'
---

The main purpose is to fetch the feedback and create a GitHub Issue for each, so it can be handled better (assigned to other people, boards, teams, etc.). It's built as a [GitHub Action](https://github.com/marketplace/actions/testflight-feedback), so it's easy to use straight away.

<div class="gallery-box">
  <div class="gallery">
    <img src="/images/owns/testflight-feedback/screenshot-example-issue-1.jpg" loading="lazy" alt="Example Issue 1">
    <img src="/images/owns/testflight-feedback/screenshot-example-issue-2.jpg" loading="lazy" alt="Example Issue 2">
  </div>
  <em>Screenshots of the <a href="https://github.com/lukas-ruzicka/testflight-feedback/issues/1">example GitHub Issue</a></em>
</div>

From a technical point of view, the tool is written in Swift and packed in a Swift package with an executable product. It's set up to be installed on a device using [Mint](https://github.com/yonaskolb/Mint). I've also added the option to run it directly as a GitHub action, so it can be used just by adding _workflow_ files to a repository. You can read more about the possibilities in the [repository](https://github.com/lukas-ruzicka/testflight-feedback).