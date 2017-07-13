```
    import logging
    import logging.handlers
    LOG_FILE = "../log/get_ios_video.log"

    def initlog():
        logger = logging.getLogger()
        hdlr = logging.handlers.TimedRotatingFileHandler(LOG_FILE, when='H', interval=1, backupCount=40)
        formatter = logging.Formatter('%(asctime)s %(levelname)s %(message)s')
        hdlr.setFormatter(formatter)
        logger.addHandler(hdlr)
        logger.setLevel(logging.NOTSET)
        return logger

    logger = initlog()
```