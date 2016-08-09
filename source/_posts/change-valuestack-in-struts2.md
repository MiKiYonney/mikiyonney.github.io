title: Change valuestack in struts2
date: 2016-08-05 16:34:43
tags:
categories: Java
---

最近写了一个Struts2的拦截器，拦截器的目标是为了对所有请求返回的实体中的字符串的字段进行处理，譬如这个字符串中如果包含了敏感词，给字符串中的这个词凭借一个样式。自定义拦截器如下：
<!--more-->

	public class MyCheckInterceptor extends AbstractInterceptor {
    private static final Logger logger = LoggerFactory.getLogger(MyCheckInterceptor.class);
    private static final String COMMON_RESP_NAME = "resp";

    @Autowired
    private AICheckBizHelper aiCheckBiz;

    @Override
    public String intercept(ActionInvocation actionInvocation) throws Exception {
        try {
             Object action = actionInvocation.getAction();
             if (!(action instanceof SomeAction)) {
                 return;
             }
             ValueStack valueStack = actionInvocation.getStack();
             Object respObj = valueStack.findValue(COMMON_RESP_NAME);

             if (respObj instanceof JoyPageWebDTO) {
                 PageWebDTO pageWebDTO = (PageWebDTO) respObj;
                 List<?> recordsList = pageWebDTO.getRecords();
                 if (CollectionUtils.isNotEmpty(recordsList)) {
                     Map<String, String> keyword2HintMsgMap = mapKeyword2RuleResultDTO(recordsList);
                     if (keyword2HintMsgMap.keySet().isEmpty()) {
                         return;
                     }
                     aiCheckBiz.setFieldData(recordsList, keyword2HintMsgMap);
                 }
             }
         } catch (Exception e) {
             logger.error("MyCheckInterceptor error", e);
         }
       }
        return actionInvocation.invoke();
    }

自定义拦截器的配置如下：
	
	<package name="ajax" namespace="/ajax" extends="json-default">
    <interceptors>
        <interceptor name="check" class="interceptor.MyCheckInterceptor"></interceptor>
        <interceptor-stack name="defaultInterceptorStack">
            <interceptor-ref name="check" />
            <interceptor-ref name="defaultStack" />
        </interceptor-stack>
    </interceptors>
    <default-interceptor-ref name="defaultInterceptorStack"/>

    <action name="search" class="com.action.SearchAction">
        <result type="json">
            <param name="root">resp</param>
        </result>
    </action>
 </package>

 然而，我发现，valuestack中的值一直不被改变。后来看了一下Struts2的源码实现，得知我这样处理的valueStack中的对象是被序列化之后的，修改的是原valueStack的一个拷贝。可以使用PreResultListener，在valuestack序列化之前对valuestack中的值进行处理。使用方法：

	 public class MyInterceptor extends AbstractInterceptor {
	   ...
	    public String intercept(ActionInvocation invocation) throws Exception {
	       invocation.addPreResultListener(new PreResultListener() {
	            public void beforeResult(ActionInvocation invocation, 
	                                     String resultCode) {
	                // perform operation necessary before Result execution
	            }
	       });
	    }
	   ...
	}

大功告成。





