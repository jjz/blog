#Android ListView中的addHeaderView

在ListView中有的时候会需要在头部和底部的位置需要展示一些固定的View,这个时候就需要使用AddHeaderView()和AddFooterView()方法。
在ListView中有一个方法叫做AddHeaderView(),作用是在ListView的头部中添加布局。对应的还有addFooterView()方法，在ListView的底部添加布局。
介绍下addHeaderView()的使用。
## 在setAdapter之前还是之后
addHeaderView是放在setAdapter之前还是之后呢？
先看下addHeaderView的源代码：
```
public void addHeaderView(View v, Object data, boolean isSelectable) {
        final FixedViewInfo info = new FixedViewInfo();
        info.view = v;
        info.data = data;
        info.isSelectable = isSelectable;
        mHeaderViewInfos.add(info);
        mAreAllItemsSelectable &= isSelectable;

        // Wrap the adapter if it wasn't already wrapped.
        if (mAdapter != null) {
            if (!(mAdapter instanceof HeaderViewListAdapter)) {
                mAdapter = new HeaderViewListAdapter(mHeaderViewInfos, mFooterViewInfos, mAdapter);
            }

            // In the case of re-adding a header view, or adding one later on,
            // we need to notify the observer.
            if (mDataSetObserver != null) {
                mDataSetObserver.onChanged();
            }
        }
    }
```
这里可以看到如果mAdapter不为null的情况下，会对mAdapter进行处理，用mHeaderViewInfos



