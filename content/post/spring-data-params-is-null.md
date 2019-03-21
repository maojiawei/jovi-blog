---
title: "Spring Data参数为空解决方案"
date: 2018-01-20T12:41:00+08:00
draft: false
tags: ["spring"]
categories: "microservice"
toc: false
---
spring data在很大程度上可以简化我们的代码，提高代码的开发质量。但最近在项目中遇到了以下问题，特别做下记录与大家分享。  
## 场景
在项目可能存在以下场景,假设我有一个User用户，我想所有用户或根据组编号统计用户。我们一般可以做一下方式，利用重载的方式做两个方法。因为如果第一个方法中groupId为空时，spring data会自动解析为groupId is null。
```java
@Getter
@Setter
@NoArgsConstructor
public class UserBO {
    /**
    * 主键
    */
    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    @Column(name = "ID")
    private Long id;
    /**
    * 用户名
    */
    @Column(name = "NAME")
    private String name;
    /**
    * 组编号
    */
    @Column(name = "GROUP_ID")
    private Long groupId;
}
```
```java
@Repository
public interface UserRepository extends CrudRepository<UserBO, Long>, JpaRepository<UserBO, Long>,PagingAndSortingRepository<UserBO, Long> ,JpaSpecificationExecutor<UserBO> {
    /**
     * 根据组编号统计用户
     * @param groupId 组编号
     * @return 
     */
    @Query("select count(user.id) from UserBO user where  user.groupId =:groupId")
    Long getAmountForAll(@Param("groupId") Long groupId);
    
    /**
     * 统计所有用户
     * @param groupId 组编号
     * @return 
     */
    @Query("select count(user.id) from UserBO user ")
    Long getAmountForAll();
}
```

## 解决方案
采用以下方法`(:groupId is null or  user.groupId =:groupId）`先判断groupId是否为空。这样即使groupId参数为空，那么就会跳过该查询条件。
```java
@Repository
public interface UserRepository extends CrudRepository<UserBO, Long>, JpaRepository<UserBO, Long>,PagingAndSortingRepository<UserBO, Long> ,JpaSpecificationExecutor<UserBO> {
    /**
     * 根据组编号统计用户
     * @param groupId 组编号
     * @return 
     */
    @Query("select count(user.id) from UserBO user where (:groupId is null or  user.groupId =:groupId）")
    Long getAmountForAll(@Param("groupId") Long groupId);
    
}
```
