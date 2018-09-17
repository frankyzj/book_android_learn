# 2.1.1 Property Animation {#211-property-animation}

属性动画非常强大，你可以定义一个动画，使对象的任何属性随时间变化，而不用考虑这个对象是否已经绘制到了屏幕上。

## 属性动画中需要指定的几个要素 {#属性动画中需要指定的几个要素}

1. Duration：默认300ms，可自定义。
2. Time interpolation：可以让动画非线性播放，即变快或者变慢。
3. Repeat count and behavior：设置动画的重复播放，还可以让动画来回反转播放（从头至尾，由尾到头）。
4. Animator sets：可以把多个动画放到一个集合里同时播放，或者顺序播放，或经过一个延迟后播放。
5. Frame refresh delay：默认每10ms刷新一帧，可自定义，但实际情况取决于系统。

## 属性动画的工作原理 {#属性动画的工作原理}

下图描述一个动画涉及到的类![](https://frankyzj.gitbooks.io/android/content/valueanimator.png "一个动画对象的相关类")

ValueAnimator类保存动画的时间线，比如动画已经执行的时间、当前属性值等。 TimeInterpolator用来设置插值方式，比如AccelerateDecelerateInterpolator，TypeEvaluator设置属性值的计算方式，比如IntEvaluator。 创建一个ValueAnimator对象，设定相关属性的初值和终值，以及持续时间。然后调用start\(\)，动画开始。 API Demos的 com.example.android.apis.animation包有相关的例子。

## Property动画和View动画的区别 {#property动画和view动画的区别}

View动画只能作用于View对象，而且只能对View对象进行缩放、旋转等操作，甚至不能改变View的背景色。 View动画改变的是View的绘制位置，而view对象仍停留在原理的位置。 Property动画则不存在上述问题。 当然，View动画更省资源还有代码。所以，开发者应当按照自己的需求选择方案。

## API概览 {#api概览}

属性动画的API主要在[android.animation](https://developer.android.com/reference/android/animation/package-summary.html)包中。

**Animator**是属性动画的基类，平常主要用以下几个类：

* ValueAnimator： 这个类包含了计算变化中的属性值和时间细节的方法，还有动画是否重复的信息、更新事件的监听器，还可以自定义评估类型。但是该类没有直接将上述计算结果应用到对象及其属性上，开发者监听相应的得变化，并书写自己的逻辑，将变化应用到相应的对象上。
* ObjectAnimator： 这个类是ValueAnimator的子类。大多数情况下，你会选用这个类，因为可以更容易地将动画效果的计算结果应用到目标对象。
* AnimatorSet： 动画效果集。

**Evaluators**这个类用来确定给定属性的值，根据Animator类提供的时间和动画的初值、终值，来计算动画过程。

* IntEvaluator：计算整数类型。
* FloatEvaluator：计算浮点类型。
* ArgbEvaluator：计算十六进制的color类型
* TypeEvaluator：用来自定义Evaluator的接口类。

**Interpolators**以下是android.view.animation包中提供的Interpolators，还可以通过实现TimeInterpolator接口来自定义。

* AccelerateDecelerateInterpolator：两头慢中间快。
* AccelerateInterpolator：逐渐加速。
* AnticipateInterpolator：先回撤，然后向前甩。
* AnticipateOvershootInterpolator：先回撤，再向前甩过终值，再回到终值。
* BounceInterpolator：弹球落地。
* CycleInterpolator：钟摆，摆动指定圈数。
* DecelerateInterpolator：先快后慢。
* LinearInterpolator：线性变化。
* OvershootInterpolator：先甩过终值再回到终值。
* TimeInterpolator：自定义Interpolator的接口。

## Animating with ValueAnimator {#animating-with-valueanimator}

1. 通过ValueAnimator类指定动画的时长和随之变化的值类型。获取ValueAnimator对象的工厂类有ofInt\(\), ofFloat\(\), ofObject\(\)。
   ```
   ValueAnimator animation = ValueAnimator.ofFloat(0f, 1f);
   animation.setDuration(1000);
   animation.start();

   ```
2. 也可以自定义值类型。
   ```
   ValueAnimator animation = ValueAnimator.ofObject(new MyTypeEvaluator(), startPropertyValue, endPropertyValue);
   animation.setDuration(1000);
   animation.start();

   ```

   因为ValueAnimation并不直接操作对象及其属性，所以上述代码并没有实际效果。但是可以通过在ValueAnimator中注册ValueAnimator.AnimatorUpdateListener等listener来监听动画生命周期中像刷新帧这样的的重要事件，通过调用getAnimatedValue\(\)方法，来获得计算后的值并应用到特定帧上。在Animation Listener中可以看到更加详细的信息。

## Animating with ObjectAnimator {#animating-with-objectanimator}

ObjectAnimator类继承ValueAnimator类，但是其中的动画相关属性值是自动更新的，不需要自己注册listener。 初始化ObjectAnimator需要制定相应的对象和字符串类型的属性名。

```
ObjectAnimator anim = ObjectAnimator.ofFloat(foo, "alpha", 0f, 1f);
anim.setDuration(1000);
anim.start();

```

正确使用ObjectAnimator需要注意以下几点：

1. 相应类中的动画相关属性必须有setter，即set
   \(\)，作为自动更新属性值的入口。
2. 在初始化ObjectAnimator的工厂方法中，如果给可变参数values...只指定一个value，那么相应的属性必须有getter，即get
   \(\)，用来获取属性的初值。
3. gettter和setter操作的属性的类型必须跟指定的初值、终值的类型一致。
4. 有时候，比如修改一个 Drawable 对象的颜色时，需要在onAnimationUpdate\(\)回调方法中调用View的invalidate\(\)方法来强制重绘这个view。调用属性的setter，比如setAlpha\(\)、setTranslationX\(\)设置新值后，会自动重绘view。

## Choreographing\(编排\) Multiple Animations with AnimatorSet {#choreographing编排-multiple-animations-with-animatorset}

AnimationSet是把一系列动画放在一起使用，也可以把多个AnimationSet放在一起使用。 下面是 APIDemos 中 Bouncing Balls 的一段代码。

1. Plays bounceAnim.
2. Plays squashAnim1, squashAnim2, stretchAnim1, and stretchAnim2 at the same time.
3. Plays bounceBackAnim.
4. Plays fadeAnim.
   ```
   AnimatorSet bouncer = new AnimatorSet();
   bouncer.play(bounceAnim).before(squashAnim1);
   bouncer.play(squashAnim1).with(squashAnim2);
   bouncer.play(squashAnim1).with(stretchAnim1);
   bouncer.play(squashAnim1).with(stretchAnim2);
   bouncer.play(bounceBackAnim).after(stretchAnim2);
   ValueAnimator fadeAnim = ValueAnimator.ofFloat(newBall,"alpha",1f,0f);
   fadeAnim.setDuration(250);
   AnimatorSet animatorSet = new AnimatorSet();
   animatorSet.play(bouncer).before(fadeAnim);
   animatorSet.start();

   ```

## Animation Listeners {#animation-listeners}

1. Animator.AnimatorListener

   * onAnimationStart\(\);
   * onAnimationEnd\(\);
   * onAnimationRepeat\(\);
   * onAnimationCancel\(\):动画取消后会引发该事件，还有OnAnimationEnd\(\)事件。

2. ValueAnimator.AnimatorUpdateListener：使用ValueAnimator时必须实现这个listener。

   * onAnimationUpdate\(\)： 动画每一帧都会引发该事件。使用getAnimatedValue\(\)方法，获得传入的ValueAnimator对象生成的计算结果，并在此事件中使用。

如果不想实现Animator.AnimatorListener接口，重写其中的所有回调方法，那么继承AnimatorListenerAdapter类，重写其中的相关方法也可以。

以下是 Bouncing Balls 中的代码片段，继承那么继承AnimatorListenerAdapter类后，只重写了onAnimationEnd\(\) 方法。

```
  ValueAnimator anim = ValueAnimator.ofFloat(new Ball(),"alpha",1f,0f);
  anim.setDuration(250);
  anim.addListener(new AnimatorListener(){
      public void onAnimationEnd(Animator animation){
        balls.remove(((ObjectAnimator)animation).getTargt());
      }
  });

```

## Animating Layout Changes to ViewGroups {#animating-layout-changes-to-viewgroups}

属性动画同样可以轻松地将动画应用于ViewGroup对象。 开发者可以通过LayoutTransition类来实现ViewGroup中layout改变的动画。往ViewGroup中添加、移除View，或者调用View的setVisibility\(\)方法时，相应view可以展示出现或者消失的动画。ViewGroup中保留的那些View也会以动画效果移动到新位置。 将以下LayoutTransition常数代表的动画效果保存在一个Animator对象中，调用setAnimator\(\)方法将该Animator对象传入一个LayoutTransition对象，从而实现相应的动画效果。

* APPEARING：View被添加或者显现出来的动画。
* CHANGE\_APPEARING：有view被添加或者显现出来时，ViewGroup中其他View的动画
* DISAPPEARING：view被移除或者隐藏的动画。
* CHANGE\_DISAPPEARING：有view被移除或隐藏时，容器中其他view的动画。

可以针对上述四种类型自定义动画或使用默认动画。

API Demos中的LayoutAnimations展现了如何自定义布局动画并应用到View对象上。在Demo中，LayoutAnimationsByDefault及其布局资源文件 layout\_animations\_by\_default.xml 展示了如何在ViewGroup中使用默认动画。即，将XML中的android:animateLayoutchanges属性的值设为true。如下例。

```
<
LinearLayout
    android:orientation="vertical"
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    android:id="@+id/verticalContainer"
    android:animateLayoutChanges="true" /
>
```

将android:animateLayoutChanges设为true，系统会自动为消失和保留的view应用动画效果。

## Using a TypeEvaluator {#using-a-typeevaluator}

要对一个自定义类型应用动画，需要通过实现TypeEvaluator接口创建相应的Evaluator。TypeEvaluator接口只有一个 evaluate\(\) 方法。这个方法运行你使用的animator实例返回动画中的属性当前时间点的值。下面是一个FloatEvaluator类的例子。

```
  pubic class FloatEvaluator implements TypeEvaluator{
    public Object evaluate(float fraction, Object startValue, Object endValue){
      float startValue = ((Number)startValue).floatValue();
      return startValue + fraction * (((Number)endValue).floatValue() - startValue);
    }
  }

```

\*注：ValueAnimator或ObjectAnimator运行时，它会计算动画已经消失的片段，并根据使用的interpolator计算插入的动画。在计算动画每个时间点的值时，不需要考虑interpolator，因为插入的点是TypeEvaluator通过fraction参数获取的。

## Using Interpolators {#using-interpolators}

fraction表示动画播放的时间，Interpolators从Animators中获取，并根据动画类型来修改这个值。系统在 android.view.animation 包有一些常用的Interpolator。开发者还可以通过实现TimeInterpolator接口来自定义Interpolator。 下例是AccelerateDecelerateInterpolator和LinearInterpolator的比较。

**AccelerateDecelerateInterpolator**

```
public float getInterpolation(float input) {
    return (float)(Math.cos((input + 1) * Math.PI) / 2.0f) + 0.5f;
}

```

**LinearInterpolator**

```
public float getInterpolation(float input) {
    return input;
}

```

下表是时间和已播放动画片段的对照表：

| ms elapsed | Elapsed fraction/Interpolated fraction \(Linear\) | Interpolated fraction \(Accelerate/Decelerate\) |
| :--- | :--- | :--- |
| 0 | 0 | 0 |
| 200 | .2 | .1 |
| 400 | .4 | .345 |
| 600 | .6 | .8 |
| 800 | .8 | .9 |
| 1000 | 1 | 1 |

## Animating Views {#animating-views}

View动画通过改变view在屏幕上的绘制位置来实现，这是在view的容器层级来操作的，因为view没有相应的属性可以操作。这导致view动画只移动view的位置而没有移动view本身。Android3.0,中，添加了新的属性和相应的getter和setter，消除了这个缺陷。 属性动画不仅改变view对象本身，而且会自动调用invalidate\(\)方法强制重绘view。新添加的属性有以下几个：

* translationX and translationY: view在x轴和y轴上偏移的值。
* rotation, rotationX, and rotationY:rotation表示在2D图形中绕中轴旋转，后两者应用于3D图形。
* scaleX and scaleY:2D图形中相对中心的缩放。
* pivotX and pivotY: 控制旋转和缩放的轴的位置。
* x and y: view相对父容器的最终位置，x=left+translationX,y=top+translationY。
* alpha:透明度，0表示不可见，1表示不透明。

要使用view的一个属性动画，只要创建一个animator并指定相应的属性即可。例如，

```
ObjectAnimator.ofFloat(myView, "rotation", 0f, 360f);

```

## Animating with ViewPropertyAnimator {#animating-with-viewpropertyanimator}

ViewPropertyAnimator提供了使用一个Animator对象同时修改view的多个属性的捷径。它的行为和ObjectAnimator类似，都是直接修改view的属性值，但是在同时修改多个属性值时更加高效，代码也更简洁易读。请看下例：

**Multiple ObjectAnimator objects**

```
ObjectAnimator animX = ObjectAnimator.ofFloat(myView,"x",50f);
ObjectAnimator animY = ObjectAnimator.ofFloat(myView,"y",100f);
AnimatorSet animSetXY = new AnimatorSet();
animSetXY.playTogether(animX, animY);
animSetXY.start();

```

**One ObjectAnimator**

```
PropertyValueHolder pvhX = PropertyValueHolder.ofFloat("x",50f);
PropertyValueHolder pvhY = PropertyValueHolder.ofFloat("y",100f);
ObjectAnimator.ofPropertyValueHolder(myView,pvhX,pvhY).start();

```

**ViewPropertyAnimator**

```
myView.animate().x(50f).y(100f);

```

## Declaring Animations in XML {#declaring-animations-in-xml}

在XML中定义动画更容易重用。从Android3.1开始，属性动画的XML文件应当放在 res/animator/文件夹中。 属性动画的几个类和在XML中的标签的对应情况如下：

* ValueAnimator -
* ObjectAnimator -
* AnimatorSet -

下例顺序播放两个动画集合，其中第一个集合同时播放两个动画。

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

当然，需要在代码中为AnimatorSet对象引用相应的XML资源。并通过SetTarget\(\)方法将所有动画设置给目标对象。例子如下：

```
AnimatorSet set = (AnimatorSet) AnimatorInflater.loadAnimator(myContext,
    R.anim.property_animator);
set.setTarget(myObject);
set.start();

```

在XML中定义动画，详情参见目录2.1.4 Animation Resources。

