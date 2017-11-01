```python
import requests
import shutil
import os


def download_jpg(url_str: str):
    _, filename = os.path.split(url_str)
    
    response = requests.get(url_str, stream=True)
    with open(os.path.join(directory, filename), 'wb') as out_file:
        shutil.copyfileobj(response.raw, out_file)
    del response

```