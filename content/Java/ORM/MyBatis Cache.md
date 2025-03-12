---
tags:
  - mybatis
title: MyBatis缓存
---
## Settings
| Setting         | Description                                                                                                                                                                                                                                                                                                                               | Valid Values         | Default |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- | ------- |
| localCacheScope | MyBatis uses local cache to prevent circular references and speed up repeated nested queries. By default (SESSION) all queries executed during a session are cached. If localCacheScope=STATEMENT local session will be used just for statement execution, no data will be shared between two different calls to the same SqlSession.<br> | SESSION \| STATEMENT | SESSION |
| cacheEnabled    | Globally enables or disables any caches configured in any mapper under this configuration.                                                                                                                                                                                                                                                | true \| false        | true    |
## See Also
- [MyBatis Cache](https://mybatis.org/mybatis-3/sqlmap-xml.html#cache)
- [聊聊MyBatis缓存机制](https://tech.meituan.com/2018/01/19/mybatis-cache.html)
