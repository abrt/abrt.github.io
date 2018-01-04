---
layout: post
title:  "Link to FAF directly from Fedora Packages"
date:   2018-01-04 10:10:00 +0100
author: Miroslav Suchý <msuchy@redhat.com>
visible: 1
categories: FAF
---
With [a last release](https://github.com/fedora-infra/fedora-packages/blob/develop/CHANGELOG.rst#400) of
[Fedora Packages](https://apps.fedoraproject.org/packages/) there is a new feature related to [FAF](https://retrace.fedoraproject.org/).

When you lookup some package. E.g., libpreport:

[![fedora_packages][fedora_packages]][fedora_packages]

Then you can notice the drop-down menu. When you click on it, you get this menu:

[![drop_down_menu][drop_down_menu]][drop_down_menu]

And when you click on "FAF" you will be redirected to FAF server, where you can see most recent crash related
to this package.

[![faf_web][faf_web]][faf_web]

Now [try this](https://apps.fedoraproject.org/packages/) with your package.

[fedora_packages]: /assets/fedora-packages/Screenshot_20180104_102305.png "Fedora Packages App."
[drop_down_menu]: /assets/fedora-packages/Screenshot_20180104_103111.png "Drop Down Menu"
[faf_web]: /assets/fedora-packages/Screenshot_20180104_103537.png "FAF Web"

