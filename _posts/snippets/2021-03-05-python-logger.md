---
layout:     post
title:      "Python Logging Snippet"
subtitle:   "Snippets"
date:       2021-02-04 15:30:00
author:     "YooJong"
header-img: "img/background/post-bg-sourcecode.jpg"
catalog: true
tags:
    - Python
    - Snippets
    - Logging
---

```python
import logging
from logging.handlers import TimedRotatingFileHandler
import os

logger = logging.getLogger(__name__)

# Formatter
simple_formatter = logging.Formatter("[%(name)s] %(message)s")
complex_formatter = logging.Formatter(
"%(asctime)s %(levelname)s [%(name)s] [%(filename)s:%(lineno)d] - %(message)s"
)

# File Handler Logging 

# Check whether the folder exists
log_dir = './logs'
if not os.path.exists(log_dir):
    os.mkdir(log_dir)

file_handler= TimedRotatingFileHandler(filename='./logs/errors.log', \ 
                                       when='midnight',\
                                       interval=1,\
                                       encoding='utf-8')
file_handler.setFormatter(complex_formatter)
file_handler.suffix = '%Y%m%d'
file_handler.setLevel(logging.ERROR)
logger.addHandler(file_handler)

# Stream Handler Logging 
stream_handler= logging.StreamHandler()
stream_handler.setFormatter(complex_formatter)
stream_handler.setLevel(logging.DEBUG)
logger.addHandler(stream_handler)


# Root logger register handler
root_logger = logging.getLogger()
root_logger.addHandler(stream_handler)
root_logger.addHandler(file_handler)
root_logger.setLevel(logging.WARNING)
```