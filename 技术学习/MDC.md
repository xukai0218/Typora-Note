

```
pattern="%d{HH:mm:ss.SSS} [companyId:%X{companyId},traceId:%X{traceId}] %-5level %class{36} %L %M - %msg%xEx"/>
```

```javaj a
package com.joinf.dbutils.client.utils;

import joptsimple.internal.Strings;
import org.slf4j.MDC;

import java.util.Collections;
import java.util.Map;
import java.util.UUID;

/**
 * MDC工具类
 *
 * @author xukai
 * @date 2022/12/30 17:30
 */
public class MdcUtil {

    /**
     * 日志追踪Id
     */
    public static final String TRACE_ID = "traceId";

    /**
     * 获取 getCopyOfContextMap
     *
     * @return java.util.Map<java.lang.String, java.lang.String>
     * @author xukai
     * @date 2023/1/3 10:22
     */
    public static Map<String, String> getCopyOfContextMap() {
        Map<String, String> result;
        try {
            result = MDC.getCopyOfContextMap();
        } catch (RuntimeException ignored) {
            result = Collections.emptyMap();
        }
        if (result == null) {
            result = Collections.emptyMap();
        }
        return result;
    }

    /**
     * 获得 traceId 值
     *
     * @author xukai
     * @date 2023/1/3 10:43
     */
    public static String getTraceIdValue() {
        try {
            return MDC.getCopyOfContextMap().get(TRACE_ID);
        } catch (RuntimeException ignored) {

        }
        return Strings.EMPTY;
    }


    /**
     * 添加请求
     *
     * @author xukai
     * @date 2022/12/30 17:33
     */
    public static String putTraceId() {
        String traceId = Strings.EMPTY;
        try {
            traceId = UUID.randomUUID().toString();
            MDC.put(TRACE_ID, traceId);
        } catch (RuntimeException ignored) {
        }
        return traceId;
    }


    /**
     * 移除
     *
     * @author xukai
     * @date 2022/12/30 17:32
     */
    public static void removeTraceId() {
        try {
            MDC.remove(TRACE_ID);
        } catch (RuntimeException ignored) {
        }
    }

    /**
     * 自定义
     *
     * @param value
     * @author xukai
     * @date 2022/12/30 17:34
     */
    public static void put(String value) {
        try {
            MDC.put(TRACE_ID, value);
        } catch (RuntimeException ignored) {
        }
    }

    /**
     * 添加
     *
     * @param key
     * @param value
     * @author xukai
     * @date 2023/1/3 09:39
     */
    public static void put(String key, String value) {
        try {
            MDC.put(key, value);
        } catch (RuntimeException ignored) {
        }
    }

    /**
     * 移除 指定key
     *
     * @param key
     * @author xukai
     * @date 2022/12/30 17:33
     */
    public static void remove(String key) {
        try {
            MDC.remove(key);
        } catch (RuntimeException ignored) {
        }
    }

}
```

```
package com.joinf.dbutils.client.interceptor;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;

/**
 * @author: lyj
 * @ClassName: WebConfigurer
 * @Date: 2020-07-03 13:38
 * @Description: TODO
 */
@Configuration
public class WebConfigurer extends WebMvcConfigurerAdapter {

    @Autowired
    private PermissionInterceptor permissionInterceptor;

//    @Resource
//    private MdcInterceptor mdcInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(permissionInterceptor).addPathPatterns("/**");
//        registry.addInterceptor(mdcInterceptor).addPathPatterns("/**");
    }
}
```



```
package com.joinf.dbutils.client.interceptor;

import com.joinf.dbutils.client.utils.MdcUtil;
import lombok.extern.slf4j.Slf4j;
import org.apache.logging.log4j.util.Strings;
import org.slf4j.MDC;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import static com.joinf.dbutils.client.utils.MdcUtil.TRACE_ID;


/**
 * MDC 拦截
 *
 * @author xukai
 * @date 2022/12/30 17:25
 */
@Slf4j
@Component
public class MdcInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        String traceId = request.getHeader(TRACE_ID);
        // 上层有调用就用上层
        if (Strings.isNotBlank(traceId)) {
            MdcUtil.put(traceId);
        } else {
            traceId = MdcUtil.putTraceId();
        }
        log.info("requestURI =  {} | traceId = {} ", request.getRequestURI(), traceId);
        return true;
    }


    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        MDC.clear();
    }
}
```



```
/**
 * 复制父线程的 MDC 到子线程
 *
 * @param task
 * @param context 主线程
 * @return java.util.concurrent.Callable<T>
 * @author xukai
 * @date 2023/1/3 10:14
 */
private static <T> Callable<T> setMdcContextMap(final Callable<T> task, final Map<String, String> context) {
    return () -> {
        if (CollectionUtil.isNotEmpty(context)) {
            MDC.setContextMap(context);
        }
        try {
            return task.call();
        } finally {
            if (CollectionUtil.isNotEmpty(context)) {
                MDC.clear();
            }
        }
    };
}

/**
 * 复制父线程的 MDC 到子线程
 *
 * @param task
 * @param context 主线程
 * @return java.util.concurrent.Callable<T>
 * @author xukai
 * @date 2023/1/3 10:14
 */
public static <T> Runnable setMdcContextMap(final Runnable task, final Map<String, String> context) {
    return () -> {
        if (CollectionUtil.isNotEmpty(context)) {
            MDC.setContextMap(context);
        }
        try {
            task.run();
        } finally {
            if (CollectionUtil.isNotEmpty(context)) {
                MDC.clear();
            }
        }
    };
}



```

```
CompletableFuture.runAsync(ThreadPoolUtils.setMdcContextMap(() -> log.warn(builder.toString(), (Object) logParams.toArray(new String[0])), context));



@Override
public void execute(Runnable command) {
    super.execute(ThreadPoolUtils.setMdcContextMap(command, MdcUtil.getCopyOfContextMap()));
}
```