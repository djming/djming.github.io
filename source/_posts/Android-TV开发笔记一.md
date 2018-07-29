---
title: Android TV开发笔记一
date: 2017-10-18 23:38:14
tags:
    - Android
    - TV 开发
---

今天在开发TV Launcher时使用RecyclerView实现界面时遇到了遥控器无法对焦的问题，但连接上鼠标后没有问题，因此需要配置RecyclerView的Item获取焦点。
<!--more-->
一般的做法是将RecyclerView与VIewHolder的itemView均设为可点击，然后对焦点变更时间进行监听，当RecyclerView获取到焦点时分发给item：

```  java
public MyAdapter extends RecyclerView.Adapter<MyViewHolder> {
     ...
    class MyViewHolder extends RecyclerView.ViewHolder {
        MyViewHolder(View itemView){
            super(itemView);
            itemView.setFocusable(true);
        }
    }
}
...
/*
*这里也可以在xml文件中设置focusable = "true"
*/
RecyclerView rv = (RecyclerView) findViewById(R.id.recyclerView);
rv.setFocusable(true);

//RecyclerView监听焦点改变事件
rv.setOnFocusChangeListener(new View.OnFocusChangeListener() {
            @Override
            public void onFocusChange(View v, boolean hasFocus) {
                if (hasFocus){
                     //先判断是否有子控件，防止空指针。
                    if (rv.getChildCount() > 0){
                        //我默认使第一个item获取焦点，可以任意修改
                        irv.getChildAt(0).requestFocus();
                    }
                }
            }
        });
```
以上代码就能使RecyclerView得到焦点后分配给子控件，解决电视遥控器无法使RecyclerView滚动的问题。

但是。。。这是以前的解决方法，今天在使用一个复杂的LayoutManager时发现子控件获取到了焦点，但不知是焦点闪烁还是什么原因导致不停按按钮才能滑动到下一个item，这个目前还没有发现原因，但是由于这个问题只在遥控器控制时出现，所以想要解决还是很简单的。

解决方法便是，禁止itemView获取焦点，然后由RecyclerView监听按键事件，当获取到遥控器按下左或右时调用smoothScrollToPosition(int position)的方法完成切换，代码如下：

``` java
rv.setOnKeyListener(new View.OnKeyListener() {
            @Override
            public boolean onKey(View v, int keyCode, KeyEvent event) {
                iif (event.getAction() == KeyEvent.ACTION_UP){
                    switch (keyCode){
                        case KEY_LEFT:
                            itemList.smoothScrollToPosition(lastPos);
                            return true;
                        case KET_RIGHT:
                            itemList.smoothScrollToPosition(nextPos);
                            return true;
                        default:
                            return false;
                    }
                }
                return false;
            }
        });
```

至于为什么会出现类似焦点闪烁的问题，有待研究，希望知道的大神们能点拨一下😂。
