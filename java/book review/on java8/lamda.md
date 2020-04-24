# lamda

- list转map

  - ### 常用方式

    ```java
    public Map<Long, String> getIdNameMap(List<Account> accounts) {
        return accounts.stream().collect(Collectors.toMap(Account::getId, Account::getUsername));
    }
    ```

  - ### 收集成实体本身map

    ```java
    public Map<Long, Account> getIdAccountMap(List<Account> accounts) {
        return accounts.stream().collect(Collectors.toMap(Account::getId, account -> account));
    }
    ```

    **account -> account**是一个返回本身的lambda表达式，其实还可以使用Function接口中的一个默认方法代替，使整个方法更简洁优雅：

    ```java
    public Map<Long, Account> getIdAccountMap(List<Account> accounts) {
        return accounts.stream().collect(Collectors.toMap(Account::getId, Function.identity()));
    }
    ```

  - ### 重复key的情况   **java.lang.IllegalStateException: Duplicate key**

    ```java
    public Map<String, Account> getNameAccountMap(List<Account> accounts) {
        return accounts.stream().collect(Collectors.toMap(Account::getUsername, Function.identity()));
    }
    ```

    这个方法可能报错（**java.lang.IllegalStateException: Duplicate key**），因为name是有可能重复的。**toMap**有个重载方法，可以传入一个合并的函数来解决key冲突问题：

    ```java
    public Map<String, Account> getNameAccountMap(List<Account> accounts) {
        return accounts.stream().collect(Collectors.toMap(Account::getUsername, Function.identity(), (key1, key2) -> key2));
    }
    ```

    这里只是简单的使用后者覆盖前者来解决key重复问题

  - ### 指定具体收集的map

    **toMap**还有另一个重载方法，可以指定一个Map的具体实现，来收集数据：

    ```java
    public Map<String, Account> getNameAccountMap(List<Account> accounts) {
        return accounts.stream().collect(Collectors.toMap(Account::getUsername, Function.identity(), (key1, key2) -> key2, LinkedHashMap::new));
    }
    ```