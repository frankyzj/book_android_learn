译自[Android开发者API Guides](https://developer.android.com/guide/topics/graphics/overview.html)

# 2.Animation and Graphics {#2animation-and-graphics}

## 2.1 Animation {#21-animation}

Android动画包括property动画和view动画。一般来讲，推荐使用property动画，因为它更灵活、拥有更多特性。另外，还可以使用Drawable动画，加载drawable资源后逐帧（frame）播放。

### 2.1.1 Property Animation {#211-property-animation}

从Android3.0（API level 11）开始引入，API主要在 android.animation 包。[戳这里看详情](https://www.gitbook.com/book/frankyzj/android/edit#/edit/master/chapter2.1.1.md)

### 2.1.2 View Animation {#212-view-animation}

又称Tween Animation，API主要在 android.view.animation 包，只能应用于View，使用相对简单。 Tween Animation是对View对象的内容（外观）进行一些简单的操作，包括改变其位置、大小、旋转、透明度等等。例如有一个TextView对象，可以对text和TextView的背景进行移动、旋转、缩放操作。 推荐使用XML来定义动画。 动画的定义包括改变的内容，开始时间和持续时长。这种改变可以顺序发生也可以同时发生。每种改变都需要一系列特定的参数去定义（改变大小时定义初始大小和最终大小等），还有一些参数是通用的（持续时长和起始时间）。有相同起始时间的动画会同时发生，顺序发生的则需要使用起始时间和持续时间来计算。 动画的XML文件放在 res/anim 文件夹。XML文件只能有一个根元素，可以是,,,, interpolator element,或者（里边包含前几种元素，也可以包含）。动画默认是同时发生的，要使其顺序发生，需要指定 startOffset 属性。 下例中，对view对象先拉伸，然后同时缩小和旋转。

```
<
set android:shareInterpolator="false"
>
<
scale
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:fromXScale="1.0"
        android:toXScale="1.4"
        android:fromYScale="1.0"
        android:toYScale="0.6"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fillAfter="false"
        android:duration="700" /
>
<
set android:interpolator="@android:anim/decelerate_interpolator"
>
<
scale
           android:fromXScale="1.4"
           android:toXScale="0.0"
           android:fromYScale="0.6"
           android:toYScale="0.0"
           android:pivotX="50%"
           android:pivotY="50%"
           android:startOffset="700"
           android:duration="400"
           android:fillBefore="false" /
>
<
rotate
           android:fromDegrees="0"
           android:toDegrees="-45"
           android:toYScale="0.0"
           android:pivotX="50%"
           android:pivotY="50%"
           android:startOffset="700"
           android:duration="400" /
>
<
/set
>
<
/set
>
```

有些属性的值可能是相对于自己，也可能是相对于父容器，所以使用时要注意其格式，例如，pivotX的‘50’表示相对父容器的‘50%’，相对自身，‘50%’就是‘50%’。 Tween Animation也可以指定Interpolator。 如果上述XML以hyperspace\_jump.xml为名保存在res/anim/文件夹中，下面是将其应用到layout中一个ImageView上的例子：

```
ImageView spaceshipImage = (ImageView) findViewById(R.id.spaceshipImage);
Animation hyperspaceJumpAnimation = AnimationUtils.loadAnimation(this, R.anim.hyperspace_jump);
spaceshipImage.startAnimation(hyperspaceJumpAnimation);

```

除了StartAnimation\(\),还可以调用Animation.setStartTime\(\)来设定开始时间，然后调用View.setAnimation\(\)将动画应用到View上。

* 注：不论动画移动或者缩放了，相应View的边界不会自动调整去适应之，所以动画可能出现在边界外且不被裁掉。但是超过了父View的边界则会被裁掉。

### 2.1.3 Drawable Animation {#213-drawable-animation}

又称Frame Animation,使用android.graphics.drawable.AnimationDrawable类中的API在代码中定义动画。在XML文件中列出动画的每一帧更简单直观一些。XML文件放在res/drawable/文件夹中。 XML文件的根节点是元素，内部是一系列，每个item定义一帧，包括drawable资源和每一帧的时长。如下例：

```
<
animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="true"
>
<
item android:drawable="@drawable/rocket_thrust1" android:duration="200" /
>
<
item android:drawable="@drawable/rocket_thrust2" android:duration="200" /
>
<
item android:drawable="@drawable/rocket_thrust3" android:duration="200" /
>
<
/animation-list
>
```

上边的 android:oneshot 属性设为true，动画播放一遍后，停在最后一帧，设为false则会循环播放。将上边XML文件命名为rocket\_thrust.xml，作为一个View的背景图片，然后播放动画，如下例：

```
AnimationDrawable rocketAnimation;

public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.main);

  ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
  rocketImage.setBackgroundResource(R.drawable.rocket_thrust);
  rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
}

public boolean onTouchEvent(MotionEvent event) {
  if (event.getAction() == MotionEvent.ACTION_DOWN) {
    rocketAnimation.start();
    return true;
  }
  return super.onTouchEvent(event);
}

```

AnimationDrawable的start\(\)方法不能再Activity的onCreate\(\)方法中调用。如果想在页面加载后立即播放动画，可以在Activity的onWindowFocusChanged\(\)方法中调用start\(\)方法。

### 2.1.4 Animation Resources {#214-animation-resources}

[戳这里看详情](https://www.gitbook.com/book/frankyzj/android/edit#/edit/master/chapter2.1.4.md)

## 2.2 2D and 3D Graphics {#22-2d-and-3d-graphics}

开发应用时，搞清对图形绘制的需求非常重要，因为不同的需求要使用不同的技术。例如，静态展示为主的APP和交互性极强的游戏APP的图形和动画技术肯定有所不同。我们在这里讨论图形绘制的一些选择和它们各自适用的场景。

### 2.2.1 Canvas and Drawables {#221-canvas-and-drawables}

Android提供了许多用于和用户交互的View widget。开发者也可以继承这些widget然后修改它们的外观和行为。 还可以使用Canvas类中的方法自定义2D渲染，或者创建Drawable对象来实现纹理按钮、逐帧动画等。

内容详见目录 2.2.1 Canvas and Drawables

### 2.2.2 Hardware Acceleration {#222-hardware-acceleration}

在Android3.0中引入，通过硬件加速可以使 Canvas APIs 绘制图形的表现大幅提升。

内容详情见目录 2.2.2 Hardware Acceleration。

### 2.2.3 OpenGL {#223-opengl}

Android通过framework APIs和NDK为OpenGL提供支持。想要给APP添加一些Canvas APIs不支持的的图形特性，或者对性能要求不高但是要求平台独立性，需要使用framework APIs。一些对图形性能要求比较高的APP，如游戏等，或者有大量可重用的本地代码时，应当使用NDK。

OpenGL with the NDK is also useful if you have a lot of native code that you want to port over to Android. For more information about using the NDK, read the docs in the docs/ directory of the NDK download.

关于NDK，详见[NDK download\(需翻墙\)](https://developer.android.com/ndk/index.html)

