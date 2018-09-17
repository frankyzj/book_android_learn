# Material Design {#material-design}

正如官网介绍中所言，Material Design 是一种视觉语言，它综合了经典的设计原则和科技上的革命性创新以及可能性。

## 三个原则 {#三个原则}

#### 使用隐喻 {#使用隐喻}

通过表面、边缘和阴影来映射现实世界，向用户暗示操作的线索。

#### 醒目、图形化、意图明确 {#醒目、图形化、意图明确}

所有这些，为用户提供良好的视觉体验和明确的“路标”。

#### 对用户操作的反应是有意义的 {#对用户操作的反应是有意义的}

对用户的操作反馈细微敏锐清晰，移动高效连贯。输入动作只能保持在当前 material 之中，不能越界。

## 设置MaterialDesign主题 {#设置materialdesign主题}

#### 1. 修改/res/values/colors.xml如下 {#1-修改resvaluescolorsxml如下}

```
xmlversion="1.0"encoding="utf-8"?
>
<
resources
>
<
color name="colorPrimary"
>
#125688
<
/color
>
<
color name="colorPrimaryDark"
>
#125688
<
/color
>
<
color name="textColorPrimary"
>
#FFFFFF
<
/color
>
<
color name="windowBackground"
>
#FFFFFF
<
/color
>
<
color name="navigationBarColor"
>
#000000
<
/color
>
<
color name="colorAccent"
>
#c8e8ff
<
/color
>
<
/resources
>
```

#### 2. 修改/res/values/dimens.xml如下 {#2-修改resvaluesdimensxml如下}

```
<
resources
>
<
dimen name="activity_horizontal_margin"
>
16dp
<
/dimen
>
<
dimen name="activity_vertical_margin"
>
16dp
<
/dimen
>
<
dimen name="tab_max_width"
>
264dp
<
/dimen
>
<
dimen name="tab_padding_bottom"
>
16dp
<
/dimen
>
<
dimen name="tab_label"
>
14sp
<
/dimen
>
<
dimen name="custom_tab_layout_height"
>
72dp
<
/dimen
>
<
/resources
>
```

#### 3. 修改/res/values/styles.xml如下，此处定义的style适用于各API版本。 {#3-修改resvaluesstylesxml如下，此处定义的style适用于各api版本。}

```
<
resources
>
<
style name="MyMaterialTheme" parent="MyMaterialTheme.Base"
>
<
/style
>
<
style name="MyMaterialTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar"
>
<
item name="windowNoTitle"
>
true
<
/item
>
<
item name="windowActionBar"
>
false
<
/item
>
<
item name="colorPrimary"
>
@color/colorPrimary
<
/item
>
<
item name="colorPrimaryDark"
>
@color/colorPrimaryDark
<
/item
>
<
item name="colorAccent"
>
@color/colorAccent
<
/item
>
<
/style
>
<
/resources
>
```

#### 4. 在/res/下新建一个文件夹命名为 values-v21。在里边新建 styles.xml，写上如下代码，作为 Android5.0 以上的主题。 {#4-在res下新建一个文件夹命名为-values-v21。在里边新建-stylesxml，写上如下代码，作为-android50-以上的主题。}

```
<
resources
>
<
style name="MyMaterialTheme"parent="MyMaterialTheme.Base"
>
<
item name="android:windowContentTransitions"
>
true
<
/item
>
<
item name="android:windowAllowEnterTransitionOverlap"
>
true
<
/item
>
<
item name="android:windowAllowReturnTransitionOverlap"
>
true
<
/item
>
<
item name="android:windowSharedElementEnterTransition"
>
@android:transition/move
<
/item
>
<
item name="android:windowSharedElementExitTransition"
>
@android:transition/move
<
/item
>
<
/style
>
<
/resources
>
```

#### 5. 在 AndroidManifest.xml 中修改application的theme。 {#5-在-androidmanifestxml-中修改application的theme。}

```
android:theme="@style/MyMaterialTheme"

```

## 1. Android Design Support 库依赖 {#1-android-design-support-库依赖}

在app的build.gradle中加入以下依赖：

```
compile 'com.android.support:appcompat-v7:25.0.0'
compile 'com.android.support:support-v4:25.0.0'
compile 'com.android.support:design:25.0.0'

```

## 2.Floating Action Button {#2floating-action-button}

```
<
android.support.design.widget.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:src="@android:drawable/ic_dialog_email" /
>
```

Android:elevation属性为 view 在空闲状态下的阴影深度，默认为6dp，需要在 api21 以上才能使用。使用 support 包可以用app:elevation 来替代，app:pressedTanslationZ为按下状态的阴影深度，默认为12dp。按钮的颜色默认为主题的强调色，也可以使用 “app:backgroundTint”修改。app:fabSize风格,有正常和小两种normal、mini两个选择，android:src 图片。

## 3.Snackbar {#3snackbar}

```
FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.test_fab);
fab.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Snackbar.make(v, "fab click", Snackbar.LENGTH_SHORT).show();
    }
});

```

## 4.CoordinatorLayout {#4coordinatorlayout}

点击浮动按钮后，弹出的Snackbar会将浮动按钮盖住。这时候需要一个CoordinatorLayout来协调 view 布局，将根布局FrameLayout改为android.support.design.widget.CoordinatorLayout 点击浮动按钮会发现浮动按钮会动态向上，给Snackbar让地方。

## 5.Toolbar {#5toolbar}

在layout.xml中加入

```
<
android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary"
        app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
        app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
>
<
TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Material Design"
        android:textSize="20sp" /
>
<
/android.support.v7.widget.Toolbar
>
```

在activity中加入

```
Toolbar mToolbar = (Toolbar) findViewById(R.id.toolbar);
 setSupportActionBar(mToolbar);

```

CoordinatorLayout 中的 view 必须是能一同协作的 view，就像 Snackbar 一样，但是 toolbar 并不属于能协作的 view，所以我们需要用一个可以协同作战的 view 来包裹上Toolbar，这就是 AppBarLayout。

```
<
android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
>
<
android.support.v7.widget.Toolbar
           .../
>
<
/android.support.design.widget.AppBarLayout
>
```

属性app:layout\_collapseMode="pin"，使得 Toolbar 中的按钮能固定在顶部。

**注：根据官方的谷歌文档，AppBarLayout目前必须是第一个嵌套在CoordinatorLayout里面的子view**

页面部分内容被 toolbar 遮住了，原因是LinearLayout并没有被设计成在 CoordinatorLayout 中协同工作的模式，为了使他们能在CoordinatorLayout协同工作，我们需要在LinearLayout加上一条属性，来告诉它的滚动属性。

```
<
LinearLayout
    ...
    app:layout_behavior="@string/appbar_scrolling_view_behavior"
    ...
    
>
```

## 6.TabLayout {#6tablayout}

通常MaterialDesign的TabLayout应该是放在顶部，（iOS 的 tab 好像基本在底部），且在阴影部分上面，所以应该放在AppBarlayout中。

```
<
android.support.design.widget.AppBarLayout ...
>
<
android.support.v7.widget.Toolbar ... /
>
<
android.support.design.widget.TabLayout
        android:id="@+id/tabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/
>
<
/android.support.design.widget.AppBarLayout
>
```

```
private void initTableLayout() {
   mTabLayout = (TabLayout) findViewById(R.id.tabLayout);
   mTabLayout.addTab(mTabLayout.newTab().setText("Tab 1"));
   mTabLayout.addTab(mTabLayout.newTab().setText("Tab 2"));
   mTabLayout.addTab(mTabLayout.newTab().setText("Tab 3"));
}

```

或者

```
private void initTableLayout() {
    TextView tabOne = (TextView) LayoutInflater.from(this).inflate(R.layout.custom_tab, null);
        tabOne.setText("One");
        tabOne.setCompoundDrawablesWithIntrinsicBounds(tabIcons[1],0,0,0);
        tabLayout.getTabAt(0).setCustomView(tabOne);
}

```

通常 tablayout 会和ViewPager一起使用 ，这时候使用`tabLayout.setupWithViewPager(viewPager);`

```
<
?xml version="1.0" encoding="utf-8"?
>
<
android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:tools="http://schemas.android.com/tools"
xmlns:app="http://schemas.android.com/apk/res-auto"
android:layout_width="match_parent"
android:layout_height="match_parent"
tools:context="com.kaka.md.MainActivity"
>
<
android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
>
<
android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_collapseMode="pin"
            android:background="?android:attr/colorPrimary"
            app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar" /
>
<
android.support.design.widget.TabLayout
            android:id="@+id/tabLayout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar" /
>
<
/android.support.design.widget.AppBarLayout
>
<
android.support.v4.view.ViewPager
        android:id="@+id/vp_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" /
>
<
android.support.design.widget.FloatingActionButton
        android:id="@+id/test_fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        app:backgroundTint="@color/colorPrimary"
        android:layout_marginBottom="30dp"
        android:layout_marginRight="30dp"
        android:src="@drawable/ic_add"/
>
<
/android.support.design.widget.CoordinatorLayout
>
```

![](https://frankyzj.gitbooks.io/android/content/assets/tabMode.png)

## 7.内容滚动时，AppBarLayout隐藏 {#7内容滚动时，appbarlayout隐藏}

给Toolbar 加上属性

```
<
android.support.v7.widget.Toolbar
    ...
    app:layout_scrollFlags="scroll|enterAlways" /
>
```

也可以给 TabLayout 加上这个属性，使之在内容滚动时消失。

**注：NestedSrollView可以替换scrollView，与CoordinatorLayout 协同工作**

## 8.滑动内容 和 AppBarLayout是如何进行联系的 {#8滑动内容-和-appbarlayout是如何进行联系的}

Support Library包含了一个特殊的字符串资源**@string/appbar\_scrolling\_view\_behavior**，它和 AppBarLayout.ScrollingViewBehavior 相匹配。在 RecyclerView 或者任意支持嵌套滚动的view比如NestedScrollView 上添加 app:layout\_behavior，用来通知 AppBarLayout 这个特殊的view何时发生了滚动事件。

AppBarLayout.ScrollingViewBehavior 描述了 RecyclerView 与 AppBarLayout 之间的依赖关系。RecyclerView 的任意滚动事件都将触发 AppBarLayout 或者 AppBarLayout 里面 view 的改变。AppBarLayout 里面定义的 view 只要设置了applayout\_scrollFlags 属性，就可以在 RecyclerView 滚动事件发生的时候被触发。app:layout\_scrollFlags 属性里面必须至少启用 scroll 这个 flag，这样这个 view 才会滚动出屏幕，否则它将一直固定在顶部。可以使用的其他flag有：

* enterAlways: 只要向下拖动内容视图这个 view 就可见；
* enterAlwaysCollapsed: 定义已经消失之后何时再次显示。假设你定义了一个最小高度（minHeight），同时enterAlways 也定义了，那么view将在到达这个最小高度的时候开始显示，并且从这个时候开始慢慢展开，当滚动到顶部的时候展开完。
* exitUntilCollapsed: 定义何时退出。当你定义了一个 minHeight，这个 view 将在滚动到达这个最小高度的时候消失。

**注：要把带有 scroll flag 的 view 放在前面，这样收回的 view 才能让正常退出，而固定的 view 继续留在顶部。**

## 9.可折叠的 Toolbar {#9可折叠的-toolbar}

**该功能会屏蔽TabLayout**

![](https://frankyzj.gitbooks.io/android/content/assets/toolbar.gif)

1. 用 CollapsingToolbarLayout 包裹 Toolbar，但仍然在 AppBarLayout 中
2. 删除 Toolbar 中的 layout\_scrollFlags
3. 为 CollapsingToolbarLayout 声明 layout\_scrollFlags，并且将 layout\_scrollFlags 设置成 scroll\|exitUntilCollapsed

```
<
android.support.design.widget.AppBarLayout
    android:layout_width="match_parent"
    android:layout_height="256dp"
>
<
android.support.design.widget.CollapsingToolbarLayout
        android:id="@+id/collapsingToolbarLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_scrollFlags="scroll|exitUntilCollapsed"
>
<
android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:minHeight="?attr/actionBarSize"
            app:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light" 
            app:layout_collapseMode="pin"/
>
<
/android.support.design.widget.CollapsingToolbarLayout
>
<
/android.support.design.widget.AppBarLayout
>
```

CollapsingToolbarLayout 在展开和收缩时，标题的文字会自动过度的，可以通过 app:expandedTitleMargin 等来改变文字位置。  
**注意：一定要设置toolbar高度，toolbar高度不能是自适应了，因为我们要实现折叠toolbar，系统无法测出自适应高度，需要我们给一个值**

## 10.为 appBar 添加背景图片 {#10为-appbar-添加背景图片}

由于 CollapsingToolbarLayout 是继承 Framelayout 的，所以我们可以直接添加一个 ImageView 作为背景图片。

```
<
ImageView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:scaleType="centerCrop"
    android:src="@drawable/header" /
>
<
android.support.v7.widget.Toolbar
    ...

```

此时虽然背景已经出来了，但是蓝色的导航条依旧存在，需要在 toolbar 去掉这条属性

```
android:background="?attr/colorPrimary"

```

可以给ImageView加上视差模式

```
app:layout_collapseMode="parallax"
app:layout_collapseParallaxMultiplier="0.5"

```

也可以在最后恢复成主题色

```
<
android.support.design.widget.CollapsingToolbarLayout
    ...
    app:contentScrim="?attr/colorPrimary"
>
```

## 11.Navigation Drawer {#11navigation-drawer}

11.1 在 /layout/ 下新建xml文件,命名为main\_layout.xml，将android.support.v4.widget.DrawerLayout作为根元素，前面根元素为android.support.design.widget.CoordinatorLayout的xml文件，即content\_main.xml,使用引入到main\_layout.xml中。

```
<
android.support.v4.widget.DrawerLayout xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/drawer_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        tools:openDrawer="start"
>
<
include layout="@layout/content_main" /
>
<
android.support.design.widget.NavigationView
            android:id="@+id/nav_view"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            android:fitsSystemWindows="true"
            app:headerLayout="@layout/nav_header_main2"
            app:menu="@menu/activity_main2_drawer" /
>
<
/android.support.v4.widget.DrawerLayout
>
```

openDrawer设为start，从左面滑动。

DrawerLayout 中分两部分组成，一部分是 content，就是我们需要的主布局内容，另一部分是我们的抽屉的布局。NavigationView 中有顶部头和标签：

```
app:headerLayout="@layout/nav_header_main2"
app:menu="@menu/activity_main2_drawer"

```

/layout/nav\_header\_main2.xml

```
<
?xml version="1.0" encoding="utf-8"?
>
<
LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="200dp"
    android:background="@drawable/ic_draw"
    android:gravity="bottom"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:theme="@style/ThemeOverlay.AppCompat.Dark"
>
<
ImageView
        android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingTop="0dp"
        android:background="@drawable/ic_people" /
>
<
TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:paddingTop="10dp"
        android:text="KaKa"
        android:textAppearance="@style/TextAppearance.AppCompat.Body1" /
>
<
TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="I love you, Not for what you are, But for what I am When I am with you." /
>
<
/LinearLayout
>
```

#### 11.2 创建菜单 {#112-创建菜单}

菜单元素是放在group标签之下，同时声明每次只能有一个item被选中：

```
<
?xml version="1.0" encoding="utf-8"?
>
<
menu xmlns:android="http://schemas.android.com/apk/res/android"
>
<
group android:checkableBehavior="single"
>
<
item
            android:id="@+id/nav_camera"
            android:icon="@drawable/ic_menu_camera"
            android:title="Import" /
>
<
item
            android:id="@+id/nav_gallery"
            android:icon="@drawable/ic_menu_gallery"
            android:title="Gallery" /
>
<
item
            android:id="@+id/nav_slideshow"
            android:icon="@drawable/ic_menu_slideshow"
            android:title="Slideshow" /
>
<
item
            android:id="@+id/nav_manage"
            android:icon="@drawable/ic_menu_manage"
            android:title="Tools" /
>
<
/group
>
<
item android:title="Communicate"
>
<
menu
>
<
item
                android:id="@+id/nav_share"
                android:icon="@drawable/ic_menu_share"
                android:title="Share" /
>
<
item
                android:id="@+id/nav_send"
                android:icon="@drawable/ic_menu_send"
                android:title="Send" /
>
<
/menu
>
<
/item
>
<
/menu
>
```

为了防止页面被遮盖，在 DrawerLayout加入 app:layout\_behavior="@string/appbar\_scrolling\_view\_behavior" 属性使之协调（好像没什么区别）。

#### 11.3 Java初始化 {#113-java初始化}

```
private void initDraw() {
        DrawerLayout drawer = (DrawerLayout) findViewById(R.id.drawer_layout);
        ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(
                this, drawer, mToolbar, R.string.navigation_drawer_open,                                               R.string.navigation_drawer_close);
        drawer.setDrawerListener(toggle);
        toggle.syncState();

        NavigationView navigationView = (NavigationView) findViewById(R.id.nav_view);
        navigationView.setNavigationItemSelectedListener(new NavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(MenuItem item) {
                // Handle navigation view item clicks here.
                int id = item.getItemId();

                if (id == R.id.nav_camera) {
                    // Handle the camera action
                } else if (id == R.id.nav_gallery) {

                } else if (id == R.id.nav_slideshow) {

                } else if (id == R.id.nav_manage) {

                } else if (id == R.id.nav_share) {

                } else if (id == R.id.nav_send) {

                }

                DrawerLayout drawer = (DrawerLayout) findViewById(R.id.drawer_layout);
                drawer.closeDrawer(GravityCompat.START);
                return true;
            }
        });

```

## 12. 下拉刷新 SwipeRefreshLayout {#12-下拉刷新-swiperefreshlayout}

在NestedScrollView外在包裹一层SwipeRefreshLayout

```
<
android.support.v4.widget.SwipeRefreshLayout
        android:id="@+id/refresh"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
>
<
android.support.v4.widget.NestedScrollView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:fillViewport="true"
            
>

            .....
        
<
/android.support.v4.widget.NestedScrollView
>
<
/android.support.v4.widget.SwipeRefreshLayout
>
```

初始化监听器

```
private void initRefresh() {
        mSwipeRefreshLayout = (SwipeRefreshLayout) findViewById(R.id.refresh);
        mSwipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                refreshContent();
            }
        });
    }

    private void refreshContent() {
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                mSwipeRefreshLayout.setRefreshing(false);
            }
        }, 2000);
    }
```



