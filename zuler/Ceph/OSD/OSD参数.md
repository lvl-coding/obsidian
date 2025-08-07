```cpp
Option("osd_debug_inject_dispatch_delay_probability", Option::TYPE_FLOAT, Option::LEVEL_DEV)
.set_default(0)
.set_description(""),

Option("osd_debug_inject_dispatch_delay_duration", Option::TYPE_FLOAT, Option::LEVEL_DEV)
.set_default(.1)
.set_description(""),
```
可指定两个参数对OSD中注入延时，使OSD.3中50%比例的io在dispatch的队列中等待3秒的命令如下
```bash
ceph tell osd.3 injectargs '--osd_debug_inject_dispatch_delay_probability 0.5 --osd_debug_inject_dispatch_delay_duration 3'
```