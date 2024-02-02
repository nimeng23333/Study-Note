由于font awsome的库在css之前加载，所以在生产模式的时候有可能会产生屏闪，一个超大的图标，只需要在_app.js文件加上以下内容

```
import '@fortawesome/fontawesome-svg-core/styles.css';
import { config } from '@fortawesome/fontawesome-svg-core';
config.autoAddCss = false;
```
