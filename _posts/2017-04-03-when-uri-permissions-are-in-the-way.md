---
layout: post
title: When URI permissions are in the way
lead: "Since Android's ban on file:// URIs it became a bit harder for apps to use custom notification sounds. Read how I modified FileProvider to do the heavy lifting for you."
tags: ["android", "content-uri"]
readtime: true
---

Last year I read the blog post [Notifications, Sounds, Android 7.0, and Aggravation](https://commonsware.com/blog/2016/09/07/notifications-sounds-android-7p0-aggravation.html) by the always well-informed Mark Murphy. In it Mark describes how the [ban on file: URIs](https://commonsware.com/blog/2016/03/14/psa-file-scheme-ban-n-developer-preview.html) has made things difficult for developers who want to use custom notification sounds. Apparently, the notification subsystem wasn't really prepared for `content:` URIs taking over. It's currently not possible to use [Notification.Builder#setSound(Uri)](https://developer.android.com/reference/android/app/Notification.Builder.html#setSound(android.net.Uri)) with a `content:` URI pointing to a `ContentProvider` that hasn't been exported. That means [`FileProvider`](https://developer.android.com/reference/android/support/v4/content/FileProvider.html), the standard solution when it comes to exposing files via `content:` URIs, won't work.

Mark lists a couple of options to work around the problem. Personally, I thought the option he named "The Axe", using a custom `ContentProvider` without URI permissions, was the cleanest solution.
For unrelated reasons I was reading the source code of `FileProvider` at that time. With the blog post in mind I thought it should be fairly easy to change the class to support that use case. And that's what I did. The result was [`PublicFileProvider`](https://github.com/cketti/PublicFileProvider), a modified version of `FileProvider` that doesn't require URI permissions and that takes special care to only expose files read-only.

For the last 7 months the project was gathering dust on GitHub. But the other day I stumbled across [this entry](https://issuetracker.google.com/issues/36524161) in the bug tracker for the Android O Preview where Mark [reiterates](https://code.google.com/p/android/issues/detail?id=221899) that the permission issue should be fixed in the platform rather than worked around by app developers. I agree, but I'm not confident this will be fixed in Android O. Even if it is, there's still Android 7.0/7.1 we have to deal with. So there might still be some demand for something like `PublicFileProvider`. Which is why I added a bit more documentation and published the library on [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22public-fileprovider%22). If you run into permission issues with custom notification sounds give `PublicFileProvider` a try.

Check out [PublicFileProvider on GitHub](https://github.com/cketti/PublicFileProvider). Feedback is always welcome. Open an issue on GitHub or reach out via Twitter. I'm [@cketti](https://twitter.com/cketti).
Please also let me know if you find another use case for this library 🙂
