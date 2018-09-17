# 2.1.4 Animation Resources {#214-animation-resources}

包括Property Animation 和 View Animation，View Animation 又分为Tween Animation和Frame Animation。

## Property Animation {#property-animation}

##### 文件位置： {#文件位置：}

res/animator/filename.xml 文件名会被用作资源ID。

##### 资源引用: {#资源引用}

In Java: R.animator.filename In XML: @\[package:\]animator/filename

##### 语法 {#语法}

```
<
set
  android:ordering=["together" | "sequentially"]
>
<
objectAnimator
        android:propertyName="string"
        android:duration="int"
        android:valueFrom="float | int | color"
        android:valueTo="float | int | color"
        
<
!-- start()方法调用后多少毫秒开始执行动画 --
>

        android:startOffset="int"
        android:repeatCount="int"
        android:repeatMode=["repeat" | "reverse"]
        android:valueType=["intType" | "floatType"]/
>
<
animator
        android:duration="int"
        android:valueFrom="float | int | color"
        android:valueTo="float | int | color"
        android:startOffset="int"
        android:repeatCount="int"
        android:repeatMode=["repeat" | "reverse"]
        android:valueType=["intType" | "floatType"]/
>
<
set
>

        ...
    
<
/set
>
<
/set
>
```

只能有一个根节点，根节点只能是,, 或者。

##### 例子 {#例子}

以下XML文件保存在res/animator/property\_animator.xml。

```
<
set android:ordering="sequentially"
>
<
set
>
<
objectAnimator
            android:propertyName="x"
            android:duration="500"
            android:valueTo="400"
            android:valueType="intType"/
>
<
objectAnimator
            android:propertyName="y"
            android:duration="500"
            android:valueTo="300"
            android:valueType="intType"/
>
<
/set
>
<
objectAnimator
        android:propertyName="alpha"
        android:duration="500"
        android:valueTo="1f"/
>
<
/set
>
```

使用动画时，首先要在Java代码中将xml文件填充到AnimationSet对象中，然后使用AnimationSet.setTarget\(Object\)方法把这个AnimationSet对象绑定到执行动画的对象上。如下例：

```
AnimatorSet set = (AnimatorSet) AnimatorInflater.loadAnimator(myContext,
    R.anim.property_animator);
set.setTarget(myObject);
set.start();

```

## View Animation {#view-animation}

View Animation包括Tween Animation 和逐帧动画，这两者都可以在XML中声明。

### Tween Animation {#tween-animation}

放置在res/anim/filename.xml。

##### 语法 {#语法}

```
<
?xml version="1.0" encoding="utf-8"?
>
<
set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@[package:]anim/interpolator_resource"
    
<
!-- true表示在所有子元素中应用该插值器 --
>

    android:shareInterpolator=["true" | "false"] 
>
<
alpha
        android:fromAlpha="float"
        android:toAlpha="float" /
>
<
scale
    
<
!-- 1.0表示大小不变 --
>

        android:fromXScale="float"
        android:toXScale="float"
        android:fromYScale="float"
        android:toYScale="float"
        android:pivotX="float"
        android:pivotY="float" /
>
<
translate
    
<
!-- 可以平移一个像素值，比如5；也可以相对自身的宽高平移，比如5%；也可以相对父容器的宽高平移，写作5%p --
>

        android:fromXDelta="float"
        android:toXDelta="float"
        android:fromYDelta="float"
        android:toYDelta="float" /
>
<
rotate
        android:fromDegrees="float"
        android:toDegrees="float"
        
<
!-- 写法参见上面translate --
>

        android:pivotX="float"
        android:pivotY="float" /
>
<
set
>

        ...
    
<
/set
>
<
/set
>
```

只能有一个根元素，可以是,,,, 或者。

##### 例子 {#例子}

以下XML保存在res/anim/hyperspace\_jump.xml。

```
<
set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false"
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
set
        android:interpolator="@android:anim/accelerate_interpolator"
        android:startOffset="700"
>
<
scale
            android:fromXScale="1.4"
            android:toXScale="0.0"
            android:fromYScale="0.6"
            android:toYScale="0.0"
            android:pivotX="50%"
            android:pivotY="50%"
            android:duration="400" /
>
<
rotate
            android:fromDegrees="0"
            android:toDegrees="-45"
            android:toYScale="0.0"
            android:pivotX="50%"
            android:pivotY="50%"
            android:duration="400" /
>
<
/set
>
<
/set
>
```

在Java代码中使用的方法如下：

```
ImageView img = (ImageView)findViewById(R.id.img_view);
Animation anim = AnimationUtils.loadAnimation(this, R.anim.hyperspace_jump);
img.startAnimation(anim);

```

### Custom interpolators {#custom-interpolators}

放置在res/anim/filename.xml。 你可以通过在XML中创建自己的interpolator资源文件来自定义interpolator，或者对现有interpolator的属性进行调整，例如，调整 AnticipateInterpolator的加速速率，调整CycleInterpolator循环的次数。

##### 语法 {#语法}

```
<
?xml version="1.0" encoding="utf-8"?
>
<
InterpolatorName xmlns:android="http://schemas.android.com/apk/res/android"
    android:attribute_name="value"
    /
>
```

##### ELEMENTS {#elements}

注意在XML中定义的 Interpolator ，必须是小写字母开头。 以下是各个Interpolator中可以修改的属性。

```
<
accelerateDecelerateInterpolator
>

The rate of change starts and ends slowly but accelerates through the middle.
No attributes.


<
accelerateInterpolator
>

The rate of change starts out slowly, then accelerates.
attributes:

android:factor
Float. The acceleration rate (default is 1).

<
anticipateInterpolator
>

The change starts backward then flings forward.
attributes:

android:tension
Float. The amount of tension to apply (default is 2).

<
anticipateOvershootInterpolator
>

The change starts backward, flings forward and overshoots the target value, then settles at the final value.
attributes:

android:tension
Float. The amount of tension to apply (default is 2).
android:extraTension
Float. The amount by which to multiply the tension (default is 1.5).

<
bounceInterpolator
>

The change bounces at the end.
No attributes


<
cycleInterpolator
>

Repeats the animation for a specified number of cycles. The rate of change follows a sinusoidal pattern.
attributes:

android:cycles
Integer. The number of cycles (default is 1).

<
decelerateInterpolator
>

The rate of change starts out quickly, then decelerates.
attributes:

android:factor
Float. The deceleration rate (default is 1).

<
linearInterpolator
>

The rate of change is constant.
No attributes.


<
overshootInterpolator
>

The change flings forward and overshoots the last value, then comes back.
attributes:

android:tension
Float. The amount of tension to apply (default is 2).

```

##### 例子 {#例子}

```
<
?xml version="1.0" encoding="utf-8"?
>
<
overshootInterpolator xmlns:android="http://schemas.android.com/apk/res/android"
    android:tension="7.0"
    /
>
```

```
<
scale xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@anim/my_overshoot_interpolator"
    android:fromXScale="1.0"
    android:toXScale="3.0"
    android:fromYScale="1.0"
    android:toYScale="3.0"
    android:pivotX="50%"
    android:pivotY="50%"
    android:duration="700" /
>
```

### Frame animation {#frame-animation}

放置在res/drawable/filename.xml。

##### 语法 {#语法}

```
<
?xml version="1.0" encoding="utf-8"?
>
<
animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot=["true" | "false"] 
>
<
item
        android:drawable="@[package:]drawable/drawable_resource_name"
        android:duration="integer" /
>
<
/animation-list
>
```

##### ELEMENTS {#elements}

```
<
animation-list
>

Required. This must be the root element. Contains one or more 
<
item
>
 elements.
attributes:

android:oneshot
Boolean. "true" if you want to perform the animation once; "false" to loop the animation.

<
item
>

A single frame of animation. Must be a child of a 
<
animation-list
>
 element.
attributes:

android:drawable
Drawable resource. The drawable to use for this frame.
android:duration
Integer. The duration to show this frame, in milliseconds.

```

##### 例子 {#例子}

以下XML文档保存在res/anim/rocket.xml

```
<
?xml version="1.0" encoding="utf-8"?
>
<
animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false"
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

```
ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
rocketImage.setBackgroundResource(R.drawable.rocket_thrust);

rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
rocketAnimation.start();
```



