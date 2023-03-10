# Executes python scripts in different environments without writing temp files to disk, it can pass and receive any variable, multiprocessing can be executed from everywhere, not only at top-level

## pip install callpyfile


```python
# Create a file called callc1.py
import sys
from callpyfile import run_py_file  # use this function to call the pyfile
from a_cv_imwrite_imread_plus import open_image_in_cv
# an example with OpenCV
pics = [
    "https://github.com/hansalemaos/screenshots/raw/main/gimages/elephant.png",
    "https://github.com/hansalemaos/screenshots/raw/main/gimages/dl.png",
    "https://github.com/hansalemaos/screenshots/raw/main/gimages/house.png",
]
allpics = []
allpics_ = [open_image_in_cv(x) for x in pics]
for p in range(100):
    allpics.extend(allpics_)


# Execute the py file
resu = run_py_file(
    variables={
        "imagelist": allpics
    },  # The keys will be the global variables in the called pyfile
    pyfile=r"C:\Users\Gamer\anaconda3\envs\dfdir\callc2.py",  # The pyfile
    activate_env_command="",  # if you want to activate another environment, you can pass something like this (Anaconda): activate_env_command = ['activate.bat', 'otherenv']
    pythonexe=sys.executable,  # python.exe from your active environment
    raise_exception=False,  # if raise_exception is False, the function returns None if an Exception occurs during the Execution
)
```



```python
# Create another file called callc2.py

import time
import cv2
from pathos.multiprocessing import ProcessingPool as Pool  # By the way: good stuff
import numpy as np
from callpyfile import to_stdout  # import the decorator


# Some CV stuff
def do_cv2_stuff(img):
    kernel = np.ones((5, 5), np.float32) / 25
    dst = cv2.filter2D(img, -1, kernel)
    return dst


@to_stdout(kill_if_exception=True) # optional, kills the process using os._exit()
def exemu():
    pool = Pool(nodes=5)
    res = pool.amap(
        do_cv2_stuff, imagelist
    )  # imagelist is not defined yet, but it doesn't matter because the decorator will create global variables from the dict you'have passed: {"imagelist": allpics},
    while not res.ready():
        time.sleep(0.001)
    allb = res.get()
    return allb


if __name__ == "__main__":
    exemu()
```



```python
# Run  callc1.py

print(allpics[0][:1])
[[[255 255 255]
  [255 255 255]
  [255 255 255]
  ...
  [ 54  53  50]
  [ 57  55  52]
  [ 59  57  54]]]

print(resu[0][:1])
[[[255 255 255]
  [255 255 255]
  [255 255 255]
  ...
  [ 59  58  55]
  [ 56  54  51]
  [ 56  55  52]]]
```

