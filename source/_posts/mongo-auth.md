title: MongoDB 认证和授权
date: 2015-09-22 13:34
categories:
- MongoDB
tags:
- MongoDB
- test
---

Mongodb 3.0提供了多种认证机制,其中SCRAM-SHA-1(challenge and response mechanism)是默认的,在3.0之前,MONGODB-CR(MongoDB Challenge and Response)是默认的,
同时还提供了x509,LDAP等其它认证方式.

# 参考资料：

- [1]: 认证和授权的区别:https://en.wikipedia.org/wiki/Authentication#Authorization
- [2]: http://docs.mongodb.org/master/core/authentication/#security-authentication-mechanisms
