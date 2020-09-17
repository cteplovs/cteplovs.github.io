---
author: christeplovs
comments: true
layout: post
title: "This I Learned: reloading python libraries"
---

```
!pip install --upgrade seaborn
from importlib import reload
sns = reload(sns)
```