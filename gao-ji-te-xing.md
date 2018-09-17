# .Application的使用 {#5application的使用}

Applicaiton类的生命周期是整个程序中最长的，就等于整个程序的生命周期。 Application是全局单例的，在各处使用getApplication\(\)获得的都是同一个对象。所以用来进行数据传递、数据共享、数据缓存等操作。

### 自定义Application {#自定义application}

通常情况下，系统会自动创建。自定义时，创建一个类继承Application类，并**在Manifest.xml的标签中将name属性指定为创建的类的全名称即可**。

### 用Application进行数据缓存 {#用application进行数据缓存}

在Application中，可以使用HashMap进行数据的传递和缓存。比如，一个Activity从服务器获取数据后，可以缓存在Application。从其他Activity再回来时，就可以直接使用缓存好的数据了。

### 自定义application {#自定义application}

启动Application时，系统会创建一个PID，即进程ID，所有的Activity就会在此进程上运行。那么我们在Application创建的时候初始化全局变量，同一个应用的所有Activity都可以取到这些全局变量的值，换句话说，我们在某一个Activity中改变了这些全局变量的值，那么在同一个应用的其他Activity中值就会改变。

使用示例：

```
/** 
 * 全局Application，为什么要用Application，主要是因为这就像一个session， 
 * 用于临时保存各种传值、服务器url，如果用数据库或者其他的操作来保存这些 
 * 数据的话管理起来就很繁琐，而且也不利于程序的运行速度。 
 *  
 */  
package com.example.utils;  

import java.util.LinkedList;  
import java.util.List;  

import android.app.Activity;  
import android.app.Application;  

public class MyApplication extends Application {  
    private UsersDBManager UsersDBManager1;  
    private SystemDBManager SystemDBManager1;  
    private SubmitDBManager submitDBManager1;  

    /** 
     * 为了完全退出程序调用方法 MyApplication.getInstance().addActivity(this); 
     * MyApplication1.getInstance().exit(); 
     */  
    private static MyApplication instance;  

    private List
<
Activity
>
 activityList = new LinkedList
<
Activity
>
();  


  /**
  * 其实用getApplication()获得的对象向下转型为MyApplication即可。
  */
    private MyApplication() {  
    }
    // 单线程下，单例模式获取唯一的MyApplication实例  
    public static MyApplication getInstance() {  
        if (null == instance) {  
            instance = new MyApplication();  
        }  
        return instance;  
    }  

    //多线程下的单例模式
    private static final TaxIMPApplication APP = new TaxIMPApplication();
    private TaxIMPApplication(){}
    public static TaxIMPApplication getApplication(){
        return APP;
    }

    /**
    *正确写法
    */
    private static TaxIMPApplication application;
    public static TaxIMPApplication getApplication(){
        return application;
    }

    // 添加Activity到容器中  
    public void addActivity(Activity activity) {  
        activityList.add(activity);  
    }  

    // 遍历所有Activity并finish  
    public void exit() {  
        for (Activity activity : activityList) {  
            activity.finish();  
        }  
        System.exit(0);  
    }         
    @Override  
    public void onCreate() {  
        // TODO Auto-generated method stub  

        super.onCreate(); //重写onCreate()时必须调用 

        UsersDBManager1 = new UsersDBManager(this);  
        SystemDBManager1 = new SystemDBManager(this);  
        submitDBManager1 = new SubmitDBManager(this);  
        // 初始化全局变量  

    }  
   void  onConfigurationChanged(Configuration newConfig){}
   void  onLowMemory(){}
}

```

在其他activity中调用：

```

    MyApplication myApplication = (MyApplication) getApplication();  
    myapplication.getInstance().addActivity(this);

```

Android程序的真正入口是Application的onCreate\(\)方法，所以，不是所有APP都需要activity。

### 生命周期 {#生命周期}

* onCreate :在创建应用程序时创建。
* onTerminate :在模拟器环境中调用，真机上不会调用。当终止应用程序对象时调用，不保证一定被调用，当程序是被内核终止以便为其他应用程序释放资源，那么将不会提醒，并且不调用应用程序的对象的onTerminate方法而直接终止进程。
* onLowMemory :当后台程序已经终止资源还匮乏时会调用这个方法。好的应用程序一般会在这个方法里面释放一些不必要的资源来应付当后台程序已经终止，前台应用程序内存还不够时的情况。
* onConfigurationChanged :配置改变时触发这个方法。



