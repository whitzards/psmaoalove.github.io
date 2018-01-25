>九宫格的RecycleView高度封装：VerticalGridFragment 

>解决的问题：
>* TV焦点丢失的问题
>* 动画不被遮盖的的问题

# 拆解前先看下布局：

>布局名称为： lb_vertical_grid_fragment.xml

布局代码如下：
```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/browse_dummy"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <android.support.v17.leanback.widget.BrowseFrameLayout
        android:id="@+id/grid_frame"
        android:focusable="true"
        android:focusableInTouchMode="true"
        android:descendantFocusability="afterDescendants"
        android:layout_width="match_parent"
        android:layout_height="match_parent" >
        <FrameLayout
            android:id="@+id/browse_grid_dock"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
        <include layout="@layout/lb_browse_title" />

    </android.support.v17.leanback.widget.BrowseFrameLayout>
</FrameLayout>
```
#VerticalGridFragment： onCreateView  生命周期做的View绑定
```
 @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
            Bundle savedInstanceState) {
        /**
         * 引入布局 lb_vertical_grid_fragment.xml
         */
        ViewGroup root = (ViewGroup) inflater.inflate(R.layout.lb_vertical_grid_fragment,
                container, false);
        /**
         * 给TitleView 设置文案和监听等
         */
        setTitleView((TitleView) root.findViewById(R.id.browse_title_group));
        return root;
    }
```

# VerticalGridView 是对RecycleView的封装
好吧 重点来了
```
  @Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        /**
         *browse_grid_dock 九宫格容器
         */
        ViewGroup gridDock = (ViewGroup) view.findViewById(R.id.browse_grid_dock);
        /**
         * mGridViewHolder 为VerticalGridView的ViewHolder
         */
        mGridViewHolder = mGridPresenter.onCreateViewHolder(gridDock);
        /**
         * 将VerticalGridView 加入 gridDock
         */
        gridDock.addView(mGridViewHolder.view);
        /***
         * 在BaseGridView 中当一个子布局在摆放好,注册回调,getGridView()就是VerticalGridView
         */
        mGridViewHolder.getGridView().setOnChildLaidOutListener(mChildLaidOutListener);

        mSceneAfterEntranceTransition = sTransitionHelper.createScene(gridDock, new Runnable() {
            @Override
            public void run() {
                setEntranceTransitionState(true);
            }
        });

        updateAdapter();
    }
```
  mGridPresenter.onCreateViewHolder(gridDock)做了一些事情，我们一起来看下VerticalGridPresenter这个类的onCreateViewHolder方法：
```
 @Override
    public final ViewHolder onCreateViewHolder(ViewGroup parent) {
        /**
         * 创建了VerticalGridView ViewHolder
         */
        ViewHolder vh = createGridViewHolder(parent);
        vh.mInitialized = false;
        /**
         * 创建了VerticalGridItemBridgeAdapter 适配器也创建了
         */
        vh.mItemBridgeAdapter = new VerticalGridItemBridgeAdapter();
        initializeGridViewHolder(vh);
        if (!vh.mInitialized) {
            throw new RuntimeException("super.initializeGridViewHolder() must be called");
        }
        return vh;
    }

    /**
     * Subclass may override this to inflate a different layout.
     */
    protected ViewHolder createGridViewHolder(ViewGroup parent) {
        View root = LayoutInflater.from(parent.getContext()).inflate(
                R.layout.lb_vertical_grid, parent, false);
        return new ViewHolder((VerticalGridView) root.findViewById(R.id.browse_grid));
    }
```

MVP设计思想深入人心啊，来来我们一起看下 VerticalGridPresenter
略看了下，我突然发现没什么放大动画及焦点状态的监听。继续看，在源码中看到了这个方法，其中有个适配器 ItemBridgeAdapter,使用还听频繁，小样就是你了
```
 @Override
    public void onBindViewHolder(Presenter.ViewHolder viewHolder, Object item) {
        if (DEBUG) Log.v(TAG, "onBindViewHolder " + item);
        ViewHolder vh = (ViewHolder) viewHolder;
        vh.mItemBridgeAdapter.setAdapter((ObjectAdapter) item);
        vh.getGridView().setAdapter(vh.mItemBridgeAdapter);
    }
```
进入 ItemBridgeAdapter 瞧瞧去
```
    final class OnFocusChangeListener implements View.OnFocusChangeListener {
        View.OnFocusChangeListener mChainedListener;

        @Override
        public void onFocusChange(View view, boolean hasFocus) {
            if (DEBUG) Log.v(TAG, "onFocusChange " + hasFocus + " " + view
                    + " mFocusHighlight" + mFocusHighlight);
            if (mWrapper != null) {
                view = (View) view.getParent();
            }
           /**
             * 获取焦点的的状态处理
             */
            if (mFocusHighlight != null) {
                mFocusHighlight.onItemFocused(view, hasFocus);
            }
            if (mChainedListener != null) {
                mChainedListener.onFocusChange(view, hasFocus);
            }
        }
    }
```
这个方法，大胆猜测就是获取焦点后的动画处理，跳进去看一下，找到了FocusHighlightHelper
```
    static class BrowseItemFocusHighlight implements FocusHighlightHandler {
        private static final int DURATION_MS = 150;

        private int mScaleIndex;
        private final boolean mUseDimmer;

        BrowseItemFocusHighlight(int zoomIndex, boolean useDimmer) {
            if (!isValidZoomIndex(zoomIndex)) {
                throw new IllegalArgumentException("Unhandled zoom index");
            }
            mScaleIndex = zoomIndex;
            mUseDimmer = useDimmer;
        }

        private float getScale(Resources res) {
            return mScaleIndex == ZOOM_FACTOR_NONE ? 1f :
                    res.getFraction(getResId(mScaleIndex), 1, 1);
        }

        @Override
        public void onItemFocused(View view, boolean hasFocus) {
            view.setSelected(hasFocus);
            getOrCreateAnimator(view).animateFocus(hasFocus, false);
        }

        @Override
        public void onInitializeView(View view) {
            getOrCreateAnimator(view).animateFocus(false, true);
        }

        private FocusAnimator getOrCreateAnimator(View view) {
            FocusAnimator animator = (FocusAnimator) view.getTag(R.id.lb_focus_animator);
            if (animator == null) {
                animator = new FocusAnimator(
                        view, getScale(view.getResources()), mUseDimmer, DURATION_MS);
                view.setTag(R.id.lb_focus_animator, animator);
            }
            return animator;
        }

    }
```


>说了这么多，总结下 VerticalGridView 放入了lb_vertical_grid_fragment.xml中的browse_grid_dock并且还创建了ViewHolder,将视图和VerticalGridFragment绑定，适配器ItemBridgeAdapter ，动画处理FocusHighlightHelper，这似乎还少一个GridLayoutManager，这个非v7里的，而是v17里的，继承RecyclerView.LayoutManager。从封装上看，v17将GridLayoutManager封到了抽象类BaseGridView,BaseGridView 继承自RecyclerView
