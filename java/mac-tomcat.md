# mac tomcat记录

1.启动tomcat时，会提示没有权限。需要进入tomcat的解压目录，比如/Users/sam/test/apache-tomcat/bin，运行sudo chmod 755 *.sh，这样就可以启动tomcat了，在idea里面配置tomcat遇到权限问题，也一样这样进行处理。 

2.配置启动时默认访问的根war包，在`host`节点下添加Context子节点，注意docBase的war名称不需要加.war后缀名：
```xml
<Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Context path="" docBase="chj" debug="0" privileged="true" reloadable="true" />
        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->
        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
      </Host>
```
