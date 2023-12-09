# Java对象空指针校验工具类

## 📔 千寻简笔记介绍

千寻简文库已开源，Gitee与GitHub搜索`chihiro-doc`，包含笔记源文件`.md`，以及PDF版本方便阅读，文库采用精美主题，阅读体验更佳，如果文章对你有帮助请帮我点一个`Star`～

更新：`支持在线阅读文章，根据发布日期分类。`

@[toc]



## 简介

对象空指针是指一个指针变量指向了内存中的空地址，也就是没有指向任何有效对象的地址。在许多编程语言中，空指针通常用特殊的值（例如NULL、nil、None等）表示。

当你使用一个空指针来访问对象的成员或调用对象的方法时，会导致空指针异常（Null Pointer Exception）或类似的错误。这是因为空指针并没有指向有效的对象，无法执行相应的操作。

空指针错误通常是由以下几种情况引起的：

1. 未初始化指针：当你声明一个指针变量但没有给它赋予有效的地址时，它的值就是空指针。如果你在使用该指针之前没有对其进行初始化，就会导致空指针异常。
2. 对象释放或销毁：如果你在释放或销毁一个对象后，仍然使用指向该对象的指针，就会得到空指针异常。这通常发生在动态内存分配的情况下，例如使用new关键字创建的对象。
3. 对象不存在：当你试图访问一个不存在的对象或已经被销毁的对象时，指向该对象的指针就会成为空指针。这可能是由于对象在之前的代码中未被正确创建或意外地被销毁。

为了避免空指针错误，你可以在使用指针之前进行有效性检查，或者使用安全的编程技术，如空指针检查、异常处理等。

## 工具类

### 1 基于若依改动

#### 1.1 调用示例

```java
    /**
     * 根据id查询文章列表
     * 
     * @param article 文章
     * @return 文章
     */
    @Override
    public Article getArticleById(Long id)
    {
        Article article = baseMapper.selectArticleById(id);
        AssertUtils.isNotEmpty(article,"抛出异常");
        return article;
    }
```

#### 1.2 AssertUtil：校验工具类

```java
package com.torinosrc.common.utils.star;

import cn.hutool.core.util.ObjectUtil;

import com.torinosrc.common.exception.ServiceException;

import java.text.MessageFormat;
import java.util.Objects;

/**
 * 校验工具类
 */
public class AssertUtil {

    //如果不是true，则抛异常
    public static void isTrue(boolean expression, String msg) {
        if (!expression) {
            throwException(msg);
        }
    }

    public static void isTrue(boolean expression, ErrorEnum errorEnum, Object... args) {
        if (!expression) {
            throwException(errorEnum, args);
        }
    }

    //如果是true，则抛异常
    public static void isFalse(boolean expression, String msg) {
        if (expression) {
            throwException(msg);
        }
    }

    //如果是true，则抛异常
    public static void isFalse(boolean expression, ErrorEnum errorEnum, Object... args) {
        if (expression) {
            throwException(errorEnum, args);
        }
    }

    //如果不是非空对象，则抛异常
    public static void isNotEmpty(Object obj, String msg) {
        if (isEmpty(obj)) {
            throwException(msg);
        }
    }

    //如果不是非空对象，则抛异常
    public static void isNotEmpty(Object obj, ErrorEnum errorEnum, Object... args) {
        if (isEmpty(obj)) {
            throwException(errorEnum, args);
        }
    }

    //如果不是非空对象，则抛异常
    public static void isEmpty(Object obj, String msg) {
        if (!isEmpty(obj)) {
            throwException(msg);
        }
    }

    public static void equal(Object o1, Object o2, String msg) {
        if (!ObjectUtil.equal(o1, o2)) {
            throwException(msg);
        }
    }

    public static void notEqual(Object o1, Object o2, String msg) {
        if (ObjectUtil.equal(o1, o2)) {
            throwException(msg);
        }
    }

    private static boolean isEmpty(Object obj) {
        return ObjectUtil.isEmpty(obj);
    }

    private static void throwException(String msg) {
        throwException(null, msg);
    }

    private static void throwException(ErrorEnum errorEnum, Object... arg) {
        if (Objects.isNull(errorEnum)) {
            errorEnum = BusinessErrorEnum.BUSINESS_ERROR;
        }
        throw new ServiceException(errorEnum.getErrorCode(), MessageFormat.format(errorEnum.getErrorMsg(), arg));
    }
}

```

#### 1.3 BusinessErrorEnum：业务校验异常码

```java
package com.torinosrc.common.utils.star;


/**
 * Description: 业务校验异常码
 */
public enum BusinessErrorEnum implements ErrorEnum {
    //==================================common==================================
    BUSINESS_ERROR(1001, "{0}"),
    //==================================chat==================================
    SYSTEM_ERROR(1001, "系统出小差了，请稍后再试哦~~"),
    ;
    private Integer code;
    private String msg;

    BusinessErrorEnum(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    @Override
    public Integer getErrorCode() {
        return code;
    }

    @Override
    public String getErrorMsg() {
        return msg;
    }

    public Integer getCode() {
        return code;
    }

    public BusinessErrorEnum setCode(Integer code) {
        this.code = code;
        return this;
    }

    public String getMsg() {
        return msg;
    }

    public BusinessErrorEnum setMsg(String msg) {
        this.msg = msg;
        return this;
    }
}
```

#### 1.4 ServiceException：修改若依业务异常

```java
package com.torinosrc.common.exception;

/**
 * 业务异常
 *
 * @author ruoyi
 */
public final class ServiceException extends RuntimeException
{
    private static final long serialVersionUID = 1L;

    /**
     * 错误码
     */
    private Integer code;

    /**
     * 错误提示
     */
    private String message;

    /**
     * 错误明细，内部调试错误
     *
     * 和 {@link CommonResult#getDetailMessage()} 一致的设计
     */
    private String detailMessage;

    /**
     * 空构造方法，避免反序列化问题
     */
    public ServiceException()
    {
    }

    public ServiceException(String message) {
        this.message = message;
    }

    public ServiceException(String message, Integer code) {
        this.message = message;
        this.code = code;
    }

    // 增加方法1
    public ServiceException(Integer errorCode, String errorMsg) {
        super(errorMsg);
        this.code = errorCode;
        this.message = errorMsg;
    }
 	// 增加方法2
    public ServiceException(Integer errorCode, String errorMsg, Throwable cause) {
        super(errorMsg, cause);
        this.code = errorCode;
        this.message = errorMsg;
    }

    public String getDetailMessage() {
        return detailMessage;
    }

    public String getMessage() {
        return message;
    }

    public Integer getCode()
    {
        return code;
    }

    public ServiceException setMessage(String message)
    {
        this.message = message;
        return this;
    }

    public ServiceException setDetailMessage(String detailMessage)
    {
        this.detailMessage = detailMessage;
        return this;
    }
}
```

#### 1.5 ErrorEnum：错误枚举类

```java
package com.torinosrc.common.utils.star;

public interface ErrorEnum {

    Integer getErrorCode();

    String getErrorMsg();
}
```

### 2 其他项改动

下面提供一个空指针校验工具类：

#### 2.1 调用示例：

```

    /**
     * 根据id查询文章列表
     * 
     * @param article 文章
     * @return 文章
     */
    @Override
    public Article getArticleById(Long id)
    {
        Article article = baseMapper.selectArticleById(id);
        AssertUtils.isNotEmpty(article,"抛出异常");
        return article;
    }
```

#### 2.2 AssertUtil：校验工具类

```java
import cn.hutool.core.util.ObjectUtil;


import java.text.MessageFormat;
import java.util.Objects;

/**
 * 校验工具类
 */
public class AssertUtil {

    //如果不是true，则抛异常
    public static void isTrue(boolean expression, String msg) {
        if (!expression) {
            throwException(msg);
        }
    }

    public static void isTrue(boolean expression, ErrorEnum errorEnum, Object... args) {
        if (!expression) {
            throwException(errorEnum, args);
        }
    }

    //如果是true，则抛异常
    public static void isFalse(boolean expression, String msg) {
        if (expression) {
            throwException(msg);
        }
    }

    //如果是true，则抛异常
    public static void isFalse(boolean expression, ErrorEnum errorEnum, Object... args) {
        if (expression) {
            throwException(errorEnum, args);
        }
    }

    //如果不是非空对象，则抛异常
    public static void isNotEmpty(Object obj, String msg) {
        if (isEmpty(obj)) {
            throwException(msg);
        }
    }

    //如果不是非空对象，则抛异常
    public static void isNotEmpty(Object obj, ErrorEnum errorEnum, Object... args) {
        if (isEmpty(obj)) {
            throwException(errorEnum, args);
        }
    }

    //如果不是非空对象，则抛异常
    public static void isEmpty(Object obj, String msg) {
        if (!isEmpty(obj)) {
            throwException(msg);
        }
    }

    public static void equal(Object o1, Object o2, String msg) {
        if (!ObjectUtil.equal(o1, o2)) {
            throwException(msg);
        }
    }

    public static void notEqual(Object o1, Object o2, String msg) {
        if (ObjectUtil.equal(o1, o2)) {
            throwException(msg);
        }
    }

    private static boolean isEmpty(Object obj) {
        return ObjectUtil.isEmpty(obj);
    }

    private static void throwException(String msg) {
        throwException(null, msg);
    }

    private static void throwException(ErrorEnum errorEnum, Object... arg) {
        if (Objects.isNull(errorEnum)) {
            errorEnum = BusinessErrorEnum.BUSINESS_ERROR;
        }
        throw new BusinessException(errorEnum.getErrorCode(), MessageFormat.format(errorEnum.getErrorMsg(), arg));
    }
}
```

#### 2.3 BusinessErrorEnum：业务校验异常码

```java

import lombok.AllArgsConstructor;
import lombok.Getter;

/**
 * Description: 业务校验异常码
 * Author: <a href="https://github.com/zongzibinbin">abin</a>
 * Date: 2023-03-26
 */
@AllArgsConstructor
@Getter
public enum BusinessErrorEnum implements ErrorEnum {
    //==================================common==================================
    BUSINESS_ERROR(1001, "{0}"),
    //==================================user==================================
    //==================================chat==================================
    SYSTEM_ERROR(1001, "系统出小差了，请稍后再试哦~~"),
    ;
    private Integer code;
    private String msg;

    @Override
    public Integer getErrorCode() {
        return code;
    }

    @Override
    public String getErrorMsg() {
        return msg;
    }
}
```

#### 2.4 BusinessException：异常实体类

```java

import lombok.Data;

@Data
public class BusinessException extends RuntimeException {
    private static final long serialVersionUID = 1L;

    /**
     *  错误码
     */
    protected Integer errorCode;

    /**
     *  错误信息
     */
    protected String errorMsg;

    public BusinessException() {
        super();
    }

    public BusinessException(String errorMsg) {
        super(errorMsg);
        this.errorMsg = errorMsg;
    }

    public BusinessException(Integer errorCode, String errorMsg) {
        super(errorMsg);
        this.errorCode = errorCode;
        this.errorMsg = errorMsg;
    }

    public BusinessException(Integer errorCode, String errorMsg, Throwable cause) {
        super(errorMsg, cause);
        this.errorCode = errorCode;
        this.errorMsg = errorMsg;
    }

    public BusinessException(ErrorEnum error) {
        super(error.getErrorMsg());
        this.errorCode = error.getErrorCode();
        this.errorMsg = error.getErrorMsg();
    }

    @Override
    public String getMessage() {
        return errorMsg;
    }

    @Override
    public synchronized Throwable fillInStackTrace() {
        return this;
    }
}

```

#### 2.5 ErrorEnum：错误枚举类

```java
public interface ErrorEnum {

    Integer getErrorCode();

    String getErrorMsg();
}
```



