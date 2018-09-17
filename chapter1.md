译自[Android API文档 android.view.View](https://developer.android.com/reference/android/view/View.html)

# View的使用 {#view的使用}

所有的View以树状结构排布在一个窗口中。可以通过代码或者XML向这个树状结构中添加View。

## 对View进行操作 {#对view进行操作}

##### 1. 设置属性setProperties。 {#1-设置属性setproperties。}

##### 2. 设置焦点setFocus，焦点一般有系统控制，可以使用requestFocus\(\)来获取焦点。 {#2-设置焦点setfocus，焦点一般有系统控制，可以使用requestfocus来获取焦点。}

##### 3. 设置事件监听器setListener。 {#3-设置事件监听器setlistener。}

##### 4. 设置可见性setVisibility。 {#4-设置可见性setvisibility。}

* 注：系统负责Views的measuring、laying out和drawing。只有继承ViewGroup时才需要重写相关方法。

## 自定义View {#自定义view}

* 注：自定义View不需要重写所有方法，大多数情况下只重写 onDraw\(android.graphics.Canvas\)即可。

| Category | Methods | Description |
| :--- | :--- | :--- |
| Creation | Constructors | There is a form of the constructor that are called when the view is created from code and a form that is called when the view is inflated from a layout file. The second form should parse and apply any attributes defined in the layout file. |
|  | onFinishInflate\(\) | Called after a view and all of its children has been inflated from XML. |
| Layout | onMeasure\(int, int\) | Called to determine the size requirements for this view and all of its children. |
|  | onLayout\(boolean, int, int, int, int\) | Called when this view should assign a size and position to all of its children. |
|  | onSizeChanged\(int, int, int, int\) | Called when the size of this view has changed. |
| Drawing | onDraw\(android.graphics.Canvas\) | Called when the view should render its content. |
| Event processing | onKeyDown\(int, KeyEvent\) | Called when a new hardware key event occurs. |
|  | onKeyUp\(int, KeyEvent\) | Called when a hardware key up event occurs. |
|  | onTrackballEvent\(MotionEvent\) | Called when a trackball motion event occurs. |
|  | onTouchEvent\(MotionEvent\) | Called when a touch screen motion event occurs. |
| Focus | onFocusChanged\(boolean, int, android.graphics.Rect\) | Called when the view gains or loses focus. |
|  | onWindowFocusChanged\(boolean\) | Called when the window containing the view gains or loses focus. |
| Attaching | onAttachedToWindow\(\) | Called when the view is attached to a window. |
|  | onDetachedFromWindow\(\) | Called when the view is detached from its window. |
|  | onWindowVisibilityChanged\(int\) | Called when the visibility of the window containing the view has changed. |

## IDs {#ids}

* 注：View的id最好在检索的View树里是独一无二的。

## Position {#position}

View本质上是一个矩形，用left和top标定左上角，用width和height标定尺寸。单位都是pixel。 用getLeft\(\)和getTop\(\)可以获取该View相对于直接父View的位置。相应的，还有getWidth\(\)、getHeight\(\)、getRight\(\)、getBottom\(\)方法。

## Size、Padding和Margins {#size、padding和margins}

关于Size，有measuredWidth和measuredHeight，表示该View在父View中的大小。用getMeasuredWidth\(\)和getMeasuredHeight\(\)获得。还有width和height（drawingWidth和drawingHeight），表示该View在屏幕上的实际大小。可以用getWidth\(\)和getHeight\(\)获取。后一对宽和高可能与前一对不一致。 Padding用来让View的内容与边界保持相应pixel的距离。主要方法有setPadding\(int,int,int,int\)和setPaddingRelative\(int,int,int,int\),以及setPaddingLeft\(\)等。 Margin则是属于ViewGroup的概念。

## Layout {#layout}

Layout过程包括measure和layout两个步骤。 measure过程在调用measure\(int，int\)中实现。该过程会从上到下遍历view树，计算每个view的尺寸并保存。 第二步发生在layout\(int，int，int，int\)方法中，仍然从上到下遍历view树，在此过程中父view根据前一步计算的尺寸安排好自己的子view。 Measure过程中，父view可能会多次调用子view的measure\(\)方法。当measure\(\)方法返回时，measuredWidth和measuredHeight确定。

在measure步骤中，子view使用了两个类，View.MeasureSpec类告诉父View如何度量和放置自己。LayoutParams基类用来表述view的宽和高。 MeasureSpec有三种模式：

* UNSPECIFIED:让父view来决定自己的尺寸。
* EXACTLY:
* AT\_MOST:

## Drawing {#drawing}

先绘制父view，后绘制子view，兄弟view按照出现在view树上的顺序绘制。如果view设置了background，那么会在调用该view的onDraw\(\)方法之前绘制background。子view的绘制顺序可以自定义。 调用invalidate\(\)可以强制重绘view。

## Event Handing and Threading {#event-handing-and-threading}

包括如下步骤

1. 接收到事件并分发给相应的view，该view将处理事件并通知相应的listener。
2. 在处理事件的过程中，如果view的边界需要改变，该view会调用requestLayout\(\)方法。
3. 在处理事件的过程中，如果view的外观需要改变，该view会调用invalidate\(\)方法。
4. 系统会自动绘制该view。
5. 注：view运行在UI线程中，在工作线程修改view需要使用Handler。

## Focus Handing {#focus-handing}

焦点一般由系统管理。通过isFocusable\(\)方法获知view的立场。改变一个view的立场可以使用setFocusable\(boolean\)方法。 在XML文件中可以通过设置nextFocusDown、nextFocusLeft、nextFocusRight、nextFocusUp属性来决定焦点的移动方向。 要使特定view获取焦点，调用requestFocus\(\)方法。

## Scrolling {#scrolling}

常用方法有scrollBy\(int, int\), scrollTo\(int, int\), 和 awakenScrollBars\(\)。

## Tags {#tags}

跟用来识别view的id不同，tag更像是绑定在view上的信息。 tag可以直接写在XML中，

```
<
View ...
      android:tag="@string/mytag_value" /
>
<
View ...
>
<
tag android:id="@+id/mytag"
      android:value="@string/mytag_value" /
>
<
/View
>
```

也可以使用setTag\(Object\) 或者setTag\(int, Object\)在代码中操作。

## Themes {#themes}

View默认使用传给他们context对象的Themes。可以使用android:theme 在XML中设置主题，或者在代码中向view的构造方法传入ContextThemeWrapper对象。 在下例中，两个view都将使用设定的主题，但是因为只设定了viewe属性的子集，所以，通过Context对象传入的主题的android:colorAccent属性的值依然会保留。

```
<
LinearLayout
    ...
    android:theme="@android:theme/ThemeOverlay.Material.Dark"
>
<
View ...
>
<
/LinearLayout
>
```

## Properties {#properties}

属性动画中会用到的ALPHA、 TRANSLATION\_X、 TRANSLATION\_Y，还有他们的getter和setter。

## Animation {#animation}

推荐使用android.animation 包中的API。 ViewPropertyAnimator类也非常有用。使用setAnimation\(Animation\)给一个View绑定动画，用startAnimation\(Animation\)开始动画。这里的动画就是指随时间缩放、旋转、平移view或者改变其alpha。该view的所有子view也会受动画影响。

## Security {#security}

调用 setFilterTouchesWhenObscured\(boolean\) 方法，或者在XML中设置FilterTouchesWhenObscured为true，可以防止view在不可见或者被toast等隐藏的情况下执行点击事件。 可以通过重写onFilterTouchEventForSecurity\(MotionEvent\)方法来定制安全措施。

