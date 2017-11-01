```
import os
import glob
newest = max(glob.iglob('*.[Mm][Pp]3'), key=os.path.getctime)
```
실험해보니 가장 최근에 수정된 녀석을 리턴한다.