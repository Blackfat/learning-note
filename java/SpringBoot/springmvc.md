### SpringMVC 处理请求流程总结

（1）请求到达服务端时首先调用 FrameworkServlet 中重写 HttpServlet 中的 service 方法，如果请求类型为 PATCH 或者为空则直接调用 FrameworkServlet 中的 processRequest 方法进行处理，否则将调用父类 HttpServlet 中的 **service** 方法；

（2）当执行了 HttpServlet 的 service 方法后，又会调用到 FrameworkServlet 中重写 HttpServlet 中的 doGet 或 doPost 方法，假设当我们的请求为 GET 类型时，会调用到 FrameworkServlet 中的 **doGet** 方法，在 doGet 方法中又会调用到它自身的 processRequest 方法，并将请求的 request 和 response 作为参数传入；

（3）在 **processRequest** 方法中会先进行一些地区和主体的属性获取和设置，然后比较核心的就是调用到 doService 方法来进行业务逻辑的处理；

（4）在 **doService** 方法中主要进行的就是公开 DIspatcherServlet 中的一些指定的属性（将 localeResolver 位置解析器和 themeResolver 主题解析器或一些其它属性设置到 request 的 attribute 中，使得它们可以通过 request 在全局被访问），还有就是通过调用 doDispatch 方法来进行实际的分发处理；

-  首先通过 getHandler 方法从 HandlerMapping 中获取 HandlerExecutionChain，即处理器执行链（这个链中包括了 Handler 处理器和 HandlerInterceptor 拦截器）。需要注意的这里一般存在三种 HandlerMapping，我们一般最常用的也就是 RequestMappingHandlerMapping（它是从 @Controller 类中的类型和方法级 @RequestMapping 注释创建 RequestMappingInfo 实例）
- 然后在通过 getHandlerAdapter 方法获取 Handler 对应的 HandlerAdapter 处理器适配器（每一种 Handler 都对应着不同的 Adapter，比如我们上面提到的那个通过 RequestMappingHandlerMapping 获取到的 Handler 所对应的适配器是 RequestMappingHandlerAdapter）
-  调用上面 getHandler 方法所返回的 HandlerExecutionChain 中 HandlerInterceptor 拦截器的 applyPreHandle 方法；
- 之后通过调用 HandlerAdapter 的 handle 方法来执行请求所对应的方法（这里的逻辑很复杂，而且不同的 Adapter 有不同的实现逻辑，一般最后是通过反射来实现方法调用的），并接收方法所返回的 ModelAndView 对象
- 调用上面 getHandler 方法所返回的 HandlerExecutionChain 中 HandlerInterceptor 拦截器的 applyPostHandle 方法
- 调用 processDispatchResult 方法来处理分发后的结果，这里的结果包括 handle 方法调用后返回的 ModelAndView 和在上面调用过程中产生的需要转换为 ModelAndView 的 Exception（需要返回给客户端的 Exception），然后会判断当前是否需要进行视图渲染，如果需要则调用 render 方法来进行视图渲染
- 在 processDispatchResult 方法中完成了 render 视图渲染后，会调用 HandlerExecutionChain 中 HandlerInterceptor 拦截器的 afterCompletion 方法。当其返回到 doDispatch 方法中时，在其 catch 方法中也会执行（保证在异常情况下 afterCompletion 方法也能够被正常执行）；
- 当完成 doDispatch 方法后就会进行层层返回，直到最后将响应发送给客户端