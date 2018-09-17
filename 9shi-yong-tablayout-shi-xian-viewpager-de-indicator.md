No need for that much code. You can do all this stuff without coding so much by using only viewpager with tablayout. Following will be the procedure: Your main Layout:

```
<
RelativeLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
>
<
android.support.v4.view.ViewPager
        android:id="@+id/pager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
>
<
/android.support.v4.view.ViewPager
>
<
android.support.design.widget.TabLayout
        android:id="@+id/tabDots"
        android:layout_alignParentBottom="true"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabBackground="@drawable/tab_selector"
        app:tabGravity="center"
        app:tabIndicatorHeight="0dp"/
>
<
/RelativeLayout
>
```

Hook up your UI elements in activity or fragment as follows: Java Code:

```
mImageViewPager = (ViewPager) findViewById(R.id.pager);
TabLayout tabLayout = (TabLayout) findViewById(R.id.tabDots);
tabLayout.setupWithViewPager(mImageViewPager, true);

```

That's it your are good to go.

Following xml resource file you will need to create in drawable folder.. tab\_indicator\_seleced.xml

```
<
?xml version="1.0" encoding="utf-8"?
>
<
layer-list xmlns:android="http://schemas.android.com/apk/res/android"
>
<
item
>
<
shape
android:innerRadius="0dp"
android:shape="ring"
android:thickness="4dp"
android:useLevel="false"
>
<
solid android:color="@color/colorAccent"/
>
<
/shape
>
<
/item
>
<
/layer-list
>

tab_indicator_default.xml


<
?xml version="1.0" encoding="utf-8"?
>
<
layer-list xmlns:android="http://schemas.android.com/apk/res/android"
>
<
item
>
<
shape
android:innerRadius="0dp"
android:shape="ring"
android:thickness="2dp"
android:useLevel="false"
>
<
solid android:color="@android:color/darker_gray"/
>
<
/shape
>
<
/item
>
<
/layer-list
>
```

tab\_selector.xml

```
<
?xml version="1.0" encoding="utf-8"?
>
<
selector xmlns:android="http://schemas.android.com/apk/res/android"
>
<
item android:drawable="@drawable/tab_indicator_selected"
android:state_selected="true"/
>
<
item android:drawable="@drawable/tab_indicator_default"/
>
<
/selector
>
```



