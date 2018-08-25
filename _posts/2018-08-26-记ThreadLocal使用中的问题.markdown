## WEB程序中使用ThreadLocal

### 前言

测试反馈一个注册接口发送了一条不应该发送的短信，无论从短信模板和代码传参中都不会发送该短信。收到该问题反馈已经是第二天，测试发现该问题莫名的好了，并且坚信昨天一定出现了。从测试中或者参数和发送短信的手机号，查看昨天的日志，发现短信确实发错了，并且接口调用正确。

### 分析 

确定问题真实有效，继续查看日志，发现数据库查询语句使用了根据短信模板ID发送短信，查看代码，确认存在如果存在模板ID则通过模板ID发送短信的逻辑，但前提是模板ID在threadLocal中存在(该threadLocal采用holder持有，并且该holder通过spring bean实例化，为整个spring生命周期唯一)，而该调用接口并没有设置threadLocal的操作，那threadLocal变量从何而来？继续查询代码threadLocal的set方法调用场景，发现确实存在。

  到这里有点怀疑是不是tomcat中请求的线程池在使用时，threadLocal未收到remove而导致线程的threaLocal未被回收，请求完成后线程池回收该线程，当新的请求过来时，该线程继续被分发，而该线程对象并没有被销毁，其线程对象上的threadLocal属性也并未发生变化，从而导致threadLocal对象互串。

### 验证

验证代码如下：

```
    private static ExecutorService executorService = Executors.newFixedThreadPool(2);
    
     ThreadLocal<String> threadLocal = new ThreadLocal<>();
        executorService.submit(() -> {
            try {
                System.out.println("Thread-1 start");
                threadLocal.set("123");
                Thread.sleep(100);
                System.out.println("Thread-1 end");

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        System.out.println("start thread #2");

        executorService.submit(() -> {
            try {
                System.out.println("Thread-2 start");
                Thread.sleep(100);
                System.out.println("Thread-2 end");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        executorService.submit(() -> {
            try {
                System.out.println("Thread-3 start");
                Thread.sleep(200);
                String str = threadLocal.get();
                System.out.println("Thread-3 end, threadLocal:" + str);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        executorService.submit(() -> {
            try {
                System.out.println("Thread-4 start");
                Thread.sleep(200);
                String str = threadLocal.get();
                System.out.println("Thread-4 end, threadLocal:" + str);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
```

结果中，thread-3的threadLocal正式thread-1设置的值，故在线程池中，共享threadLcal对象并且不调用remove方法后，可能导致threadLocal对象未被正确回收

### 修改

在调用threadLocal.set时采用在finnal代码块中电泳threadLocal.remove方法，防止方法抛出异常而threadLocal.remove方法未被调用