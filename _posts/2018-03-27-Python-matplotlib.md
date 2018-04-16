---
title: Python matplotlib
excerpt: |
  Python matplotlib
category: Python
feature_image: "https://picsum.photos/2560/600?image=872"
---
### matplotlib

```
# -*- coding: utf-8 -*-

import matplotlib.pyplot as plt
import numpy as np


n = 15
X = np.arange(n)
Y1 = (1 - X / float(n)) * np.random.uniform(0.5, 1.0, n)

plt.bar(X, +Y1)
# plt.bar(X, -Y2)

plt.xlim(-.5, n)
plt.xticks(())
plt.ylim(0, 1.25)
plt.yticks(())
plt.plot([1, 2, 3])
# plt.legend(['A simple line'])

line, = plt.plot([1, 2, 3], label='Inline label')
# Overwrite the label by calling the method.
line.set_label('Label via method')
plt.legend((line,), ('label1',))
plt.show()
```

