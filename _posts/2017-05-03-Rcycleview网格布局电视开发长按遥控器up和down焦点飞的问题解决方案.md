---
layout:     post
title:

subtitle:  Recycleview TV开发网络布局长按down和up焦点飞的问题解决方案
date:       2017-05-03
author:     LM
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Recycleview TV开发
    - Android
---

# 前言
Recycleview TV开发网络布局长按down和up焦点飞的问题解决方案

#一、GridLayoutManager onFocusSearchFailed 重写  目前我是设置了tag，大家在用的时候注意
```
	/**
     * 焦点搜索失败处理.
     */
    @Override
    public View onFocusSearchFailed(View focused, int focusDirection,
            RecyclerView.Recycler recycler, RecyclerView.State state) {
        if (focused == null) {
            return null;
        }

        int orientation = getOrientation();
        if (orientation == VERTICAL && focused instanceof ContactItemLayout) {
            ViewGroup focusParent = (ViewGroup) focused;
            if (focusParent != null) {
                int focusedParentPosition = (Integer) focusParent.getTag();
                if (focusDirection == View.FOCUS_DOWN) {
                    int lastCompletelyVisiblePosition = findLastCompletelyVisiblePosition();
                    if (focusedParentPosition > lastCompletelyVisiblePosition) {
                        return focused;
                    }
                } else if (focusDirection == View.FOCUS_UP) {
                    int firstCompletelyVisiblePosition = findFirstCompletelyVisiblePosition();
                    if (focusedParentPosition < firstCompletelyVisiblePosition) {
                        return focused;
                    }

                    if (focusedParentPosition == firstCompletelyVisiblePosition && firstCompletelyVisiblePosition == 0) {
                        return super.onFocusSearchFailed(focused, focusDirection, recycler, state);
                    }
                } else {
                    View defaultNextFocus = super.onFocusSearchFailed(focused, focusDirection, recycler, state);
                    return defaultNextFocus;
                }

                int focusedPosAtParent = focusParent.indexOfChild(focused);
                int nextFocusParentPos = getNextViewPos(focusedParentPosition, focusDirection);
                ViewGroup nextFocusParent = (ViewGroup) findViewByPosition(nextFocusParentPos);
                if (nextFocusParent != null) {
                    int nextFocusedPosAtParent = 0;
                    if (focusedPosAtParent < nextFocusParent.getChildCount()) {
                        nextFocusedPosAtParent = focusedPosAtParent;
                    } else {
                        nextFocusedPosAtParent = nextFocusParent.getChildCount() - 1;
                    }

                    return nextFocusParent.getChildAt(nextFocusedPosAtParent);
                } else {
                    return focused;
                }
            }
        }

        View defaultNextFocus = super.onFocusSearchFailed(focused, focusDirection, recycler, state);
        return defaultNextFocus;

    }
```

#二、Recycleview 处理
```
    private View mNextFocused = null;
    private View mFocusedView;
    // 由于联系人界面item布局设定间距为0，所以mItemSpaceLarge，mItemHeightMin，mItemSpace 不需要设定
    private int mItemSpaceLarge = 0;
    private int mItemHeightMin = 0;
    private int mItemSpace = 0;
   // 重写这个方法，防止RecylerView自动滚动。滚动处理在dispatchKeyEvent中
    @Override
    public void smoothScrollBy(int dx, int dy) {
    }
```

```
    @Override
    public boolean dispatchKeyEvent(KeyEvent event) {
        boolean consumed = false;
        int keyCode = event.getKeyCode();
        int action = event.getAction();
        if (action == KeyEvent.ACTION_DOWN) {
            switch (keyCode) {
                case KeyEvent.KEYCODE_DPAD_DOWN:
                case KeyEvent.KEYCODE_DPAD_UP:
                case KeyEvent.KEYCODE_DPAD_LEFT:
                case KeyEvent.KEYCODE_DPAD_RIGHT:
                    if (event.getRepeatCount() % 2 != 0) {
                        consumed = true;
                    } else {
                        consumed = handleDpadDirectionKeyEvent(event);
                    }
                    break;
            }
        }

        return consumed ? true : super.dispatchKeyEvent(event);
    }

    private int getFocusSearchDirection(int keyCode) {
        int direction = View.FOCUS_DOWN;
        if (keyCode == KeyEvent.KEYCODE_DPAD_DOWN) {
            direction = View.FOCUS_DOWN;
        } else if (keyCode == KeyEvent.KEYCODE_DPAD_UP) {
            direction = View.FOCUS_UP;
        } else if (keyCode == KeyEvent.KEYCODE_DPAD_LEFT) {
            direction = View.FOCUS_LEFT;
        } else if (keyCode == KeyEvent.KEYCODE_DPAD_RIGHT) {
            direction = View.FOCUS_RIGHT;
        }
        return direction;
    }
  public boolean handleDpadDirectionKeyEvent(KeyEvent event) {
        boolean consumed = false;
        int keyCode = event.getKeyCode();

        mFocusedView = mLayoutManager.getFocusedChild();
        if (mFocusedView != null) {
            int direction = getFocusSearchDirection(keyCode);
            View srcFocusView = mFocusedView;
            if (mFocusedView.isFocused()) {
                mNextFocused = focusSearch(mFocusedView, direction);
            } else if (mFocusedView.hasFocus()) {
                if (mFocusedView instanceof ViewGroup) {
                    srcFocusView = ((ViewGroup) mFocusedView).findFocus();
                    mNextFocused = focusSearch(srcFocusView, direction);
                }

                if (mNextFocused == null && keyCode == KeyEvent.KEYCODE_DPAD_UP) {
                    scrollToPosition(0);
                }
            }

            if (mNextFocused != null) {
                if (mNextFocused.isFocusable()) {
                //布局的类型在这里写，示列布局为item_layout
                    if (mNextFocused instanceof item_layout ) {
                        consumed = doScrollLogic(srcFocusView, mNextFocused, true, keyCode);
                        if (!consumed) {
                            if (mNextFocused != null) {
                                mNextFocused.requestFocus();
                            }
                            consumed = true;
                        }
                    } else {
                        if (keyCode == KeyEvent.KEYCODE_DPAD_UP) {
                            scrollToPosition(0);
                        }
                    }
                }
            }
        }
        return consumed;
    }

    public boolean doScrollLogic(View srcFocusView, View dstFocusView, boolean requestFocus, int keyCode) {
        if (srcFocusView == null || dstFocusView == null) {
            return false;
        }

        int[] focusViewLocation = new int[2];
        srcFocusView.getLocationInWindow(focusViewLocation);
        int[] nextFocusViewLocation = new int[2];
        dstFocusView.getLocationInWindow(nextFocusViewLocation);

        if (keyCode == KeyEvent.KEYCODE_DPAD_DOWN) {
            boolean doScroll = false;
            int scrollY = 0;
            if (srcFocusView == dstFocusView) {
                scrollY = dstFocusView.getHeight() + mItemSpace;
                doScroll = true;
            } else if (canScrollFromBottom(dstFocusView)) {
                scrollY = nextFocusViewLocation[1] + dstFocusView.getHeight() - focusViewLocation[1] - srcFocusView.getHeight();
                doScroll = true;
            }

            if (doScroll) {
                if (scrollY > 0 && mItemHeightMin > scrollY) {
                    scrollY = mItemHeightMin;
                }

                int fromTopOffset = getFromTopOffset(dstFocusView);
                if (fromTopOffset > 0) {
                    int maxScrollY = fromTopOffset - (mItemSpaceLarge + mItemSpace);
                    if (maxScrollY > 0 && scrollY > maxScrollY) {
                        scrollY = maxScrollY;
                    }
                } else {
                    scrollY = 0;
                }

                customSmoothScrollBy(0, scrollY);
                if (requestFocus) {
                    dstFocusView.requestFocus();
                }
                return true;
            }
        } else if (keyCode == KeyEvent.KEYCODE_DPAD_UP) {
            boolean doScroll = false;
            int scrollY = 0;
            if (srcFocusView == dstFocusView) {
                scrollY = -(dstFocusView.getHeight() + mItemSpace);
                doScroll = true;
            } else if (canScrollFormTop(dstFocusView)) {
                scrollY = nextFocusViewLocation[1] - focusViewLocation[1];
                doScroll = true;
            }

            if (doScroll) {
                if (scrollY < 0 && mItemHeightMin > -scrollY) {
                    // mItemHeightMin 为0 不会执行此处
                    scrollY = -mItemHeightMin;
                }

                int fromBottomOffset = getFromBottomOffset(dstFocusView);
                if (fromBottomOffset < 0) {
                    int maxScrollY = -fromBottomOffset - (mItemSpaceLarge + mItemSpace);
                    if (maxScrollY > 0 && -scrollY > maxScrollY) {
                        scrollY = -maxScrollY;
                    }
                } else {
                    scrollY = 0;
                }

                customSmoothScrollBy(0, scrollY);
                if (requestFocus) {
                    dstFocusView.requestFocus();
                }

                if (mOnScrollChangedListener != null && scrollY != 0) {
                    mOnScrollChangedListener.onScrollUp(this);
                }
                return true;
            }
        }

        return false;
    }
 public boolean canScrollFromBottom(View nextFocused) {
        if (nextFocused == null) {
            return false;
        }

        int fromBottomOffset = getFromBottomOffset(nextFocused);
        if (fromBottomOffset > 0) {
            return true;
        }

        return false;
    }

    private int getFromBottomOffset(View nextFocused) {
        if (nextFocused == null) {
            return 0;
        }

        Rect global = new Rect();
        this.getGlobalVisibleRect(global);
        int[] location = new int[2];
        nextFocused.getLocationInWindow(location);
        int bottom = location[1] + nextFocused.getHeight() + mItemSpace;
        int offset = bottom - global.bottom;
        return offset;
    }

    public int getFromTopOffset(View nextFocused) {
        if (nextFocused == null) {
            return 0;
        }

        Rect global = new Rect();
        this.getGlobalVisibleRect(global);
        int[] location = new int[2];
        nextFocused.getLocationInWindow(location);
        int top = location[1] - mItemSpace;

        int offset = top - global.top;

        return offset;
    }

    public void customSmoothScrollBy(int x, int y) {
        super.smoothScrollBy(x, y);
    }

    public boolean canScrollFormTop(View nextFocused) {
        if (nextFocused == null) {
            return false;
        }

        int fromTopOffset = getFromTopOffset(nextFocused);
        if (fromTopOffset < 0) {
            return true;
        }

        return false;
    }
```

