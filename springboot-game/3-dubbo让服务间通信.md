1、在admin-api接口中新建一个接口

```java
package com.ooqiu.gaming.service.admin.api;

import com.ooqiu.gaming.server.domain.User;

import java.util.List;

public interface UserService {
//    查询用户的接口
    public List<User> selectAll();
}

```

2、在admin项目中实现这个接口