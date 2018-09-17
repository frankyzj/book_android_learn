# 6.从一个app启动另一个app {#从一个app启动另一个app}

### 已知目标app的包名 {#已知目标app的包名}

```
Intent intent = context.getPackageManager().getLaunchIntentForPackage(appPackageName);
context.startActivity(intent);
```



