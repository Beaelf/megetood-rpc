## MRPC

a RPC framework, contains such technology stacks:`JAVA` `SpringBoot` `OKHttp` `FastJSON` `AOP`

## Overview

to do

## QuickStart

### Step 1 : Create project and add MRPC dependence

* create two maven project : **consumer** and **provider**

* add dependence in your project

```java
<dependency>
    <groupId>com.megetood</groupId>
    <artifactId>mrpc-core</artifactId>
    <version>1.0.0</version>
</dependency>
```

### Step 2 : Appoint common interfaces api between your consumer and provider

```java
package com.megetood.mrpc.demo.api;

public interface UserService {

  User findById(int id);

}
```

### Step 3 : Add code to your consumer

```java
package com.megetood.mrpc.demo.consumer;

import com.megetood.mrpc.client.Mrpc;
import com.megetood.mrpc.demo.api.User;
import com.megetood.mrpc.demo.api.UserService;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MrpcClientApplication {
	public static void main(String[] args) {
		UserService userService = Mrpc.create(UserService.class, "http://localhost:8080/");
		User user = userService.findById(1);
		System.out.println("find user id=1 from server: " + user.getName());
	}

}
```

### Step 3 : Add code to your provider

* add `EableMrpc` annotation in your project, to make MRPC work

```java
package com.megetood.mrpc.demo.provider;

import com.megetood.mrpc.autoconfigure.EnableMrpc;
import com.megetood.mrpc.api.MrpcRequest;
import com.megetood.mrpc.api.MrpcResponse;
import com.megetood.mrpc.server.MrpcInvoker;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
@EnableMrpc
public class MrpcServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(MrpcServerApplication.class, args);
	}

	@Autowired
	MrpcInvoker invoker;

	@PostMapping("/")
	public MrpcResponse invoke(@RequestBody MrpcRequest request) {
		return invoker.invoke(request);
	}
}
```

* make sure your project have specific implementation class of your common interface api, and these specific class must be managed by your spring container

```
package com.megetood.mrpc.demo.provider;

import com.megetood.mrpc.demo.api.User;
import com.megetood.mrpc.demo.api.UserService;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {
    @Override
    public User findById(int id) {
        return new User(id, "Megetood" + System.currentTimeMillis());
    }
}
```

