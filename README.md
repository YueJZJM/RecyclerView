# RecyclerView
## RecyclerView简介
 RecyclerView 只创建充满屏幕的 View。它的任务只是回收和定位屏幕上的View，其他的事情交给它的两个子类 ViewHolder 和 Adapter 处理，下面介绍这两个子类。
 ## Viewholder
Viewholder 的任务只是容纳 view 的视图。 
1. 如我在每个 view 上有一个 TextView，那么我在这个子类的构造函数中 **itemView.findbyid(TextView)**，
```
@Override
public CrimeHolder(LayoutInflater inflater, ViewGroup parent) {
    super(inflater.inflate(R.layout.list_item_crime, parent, false));
    
    itemView.setOnClickListener(this);
    mTitleTextView =  itemView.findViewById(R.id.crime_title);
    mDateTextView = itemView.findViewById(R.id.crime_data);
    }
```

2. 通过 **onClick()** 设置点击操作(实现了 View.OnClickListener 接口)
```
@Override
public void onClick(View view) {
    Toast.makeText(getActivity(),
            mCrime.getTitle() + " clicked!", Toast.LENGTH_SHORT)
            .show();
}
```
3. 此外，假设这里有个 **bind()** 函数设置 TextView 的参数
```
public void bind(Crime crime) {
    mCrime = crime;
    mTitleTextView.setText(mCrime.getTitle());
    mDateTextView.setText(mCrime.getDate().toString());
}
```
## Adapter
1. Adapter 是帮助 RecyclerView 创建 ViewHolder，Adapter 是一个控制器对象，从模型层获取数据，然后提供给 RecyclerView 显示，是沟通的桥梁。
- 在 **onCreatViewHolder()** 中创建并返回 holder
```
@Override
 public CrimeHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    LayoutInflater layoutInflater = LayoutInflater.from(getActivity());
    return new CrimeHolder(layoutInflater, parent);
}
```
2. 在 **onBindViewHolde()** 中绑定 ViewHolder 的数据至模型层
```
@Override
public void onBindViewHolder(CrimeHolder holder, int position) {
    Crime crime = mCrimes.get(position);
    holder.bind(crime);
}

```
3. 这里基于之前的假设，通过 **holder.bind()** 传入数据到 holder。
```
 @Override
public void onBindViewHolder(CrimeHolder holder, int position) {
    Crime crime = mCrimes.get(position);
    holder.bind(crime);
}
```
4. 这里通过构造函数把数据传到 Adapter 中。
```
public CrimeAdapter(List<Crime> crimes) {
    mCrimes = crimes;
}
```
5. 此外还有一个很重要的方法，得到 item 的数目
```
@Override
public int getItemCount() {
    return mCrimes.size();
}
```
## RecyclerView 里面还得干点事
1. 这里是用了 fragment，RecyclerView做的事情就是 **setAdapter()**, 
```
public View onCreateView(LayoutInflater inflater, ViewGroup container,
                         Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.fragment_crime_list, container, false);

    mCrimeRecyclerView =  view
            .findViewById(R.id.crime_recycler_view);
    mCrimeRecyclerView.setLayoutManager(new LinearLayoutManager(getActivity()));

    updateUI();

    return view;
}

private void updateUI() {
    CrimeLab crimeLab = CrimeLab.get(getActivity());
    List<Crime> crimes = crimeLab.getCrimes();

    mAdapter = new CrimeAdapter(crimes);
    mCrimeRecyclerView.setAdapter(mAdapter);
}
```
### 总结
说了这么多感觉还是好乱，现在来理一理思路。当 RecycleView 需要显示视图时，就会去找它的 Adapter 。首先，调研 Adapter 的 **getItemCount()**，得到数组的大小，接着 RecycleView 调用 Adapter **的onCreatViewHoder(ViewGroup, int)** 方法创建 ViewHolder 及其显示的视图，Adapter 就会找到并填充到 ViewHolder 的视图上。

```
sequenceDiagram
RevycleView->>Adapter: getItemCount()
Adapter->>RevycleView: 100
RevycleView->>Adapter: onCreatViewHolder()
Adapter->>RevycleView: ViewHolder
RevycleView->>Adapter: onBindViewHolder()
RevycleView->>Adapter: onCreatViewHolder()
Adapter->>RevycleView: ViewHolder
RevycleView->>Adapter: onBindViewHolder()
```


[github地址](https://github.com/YueJZJM/RecyclerView)

