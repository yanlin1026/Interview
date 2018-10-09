# spring boot  
# spring ioc
# spring mvc

<img src = assets/markdown-img-paste-20181009163137631.png width = 450><img src = assets/markdown-img-paste-20181009163137631.png width = 450>

1. 发起请求到前端控制器(DispatcherServlet)  
2. 前端控制器请求HandlerMapping查找 Handler （可以根据xml配置、注解进行查找）  
3. 处理器映射器HandlerMapping向前端控制器返回Handler，HandlerMapping会把请求映射为HandlerExecutionChain对象（包含一个Handler处理器（页面控制器）对象，多个HandlerInterceptor拦截器对象），通过这种策略模式，很容易添加新的映射策略
4. 前端控制器调用处理器适配器去执行Handler
5. 处理器适配器HandlerAdapter将会根据适配的结果去执行Handler
6. Handler执行完成给适配器返回ModelAndView
7. 处理器适配器向前端控制器返回ModelAndView （ModelAndView是springmvc框架的一个底层对象，包括 Model和view）
8. 前端控制器请求视图解析器去进行视图解析 （根据逻辑视图名解析成真正的视图(jsp)），通过这种策略很容易更换其他视图技术，只需要更改视图解析器即可
9. 视图解析器向前端控制器返回View
10. 前端控制器进行视图渲染 （视图渲染将模型数据(在ModelAndView对象中)填充到request域）
11. 前端控制器向用户响应结果
