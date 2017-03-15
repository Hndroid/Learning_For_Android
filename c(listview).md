* ### **学习ListView笔记**

### MainActivity：

```java
public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";
    private Context context;
    private ListView listView;
    private MyNewsAdapter myNewsAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        context = this;

        //1.找到对应的listView控件
        listView = (ListView) findViewById(R.id.id_listview);
        //2.找到对应的adapter
        myNewsAdapter = new MyNewsAdapter(context, AllNewsutils.getAllnews(context));
        //3.对应的listView设置对应的adapter;
        listView.setAdapter(myNewsAdapter);
    }
}
```

* ### **MyNewsAdapter**

```java
public class MyNewsAdapter extends BaseAdapter {

    private static final String TAG = "MyNewsAdapter";
    private Context context;
    private ArrayList<AllNewsBean> arrayList;

    public MyNewsAdapter(Context context, ArrayList<AllNewsBean> arrayList){
        this.context = context;
        this.arrayList = arrayList;
    }

    //作用:返回listview的item数目;
    @Override
    public int getCount() {
        return arrayList.size();
    }

    //作用:将了list集合中指定position上的bean对象返回;
    @Override
    public Object getItem(int position) {
        return null;
    }

    //作用：直接返回position
    @Override
    public long getItemId(int position) {
        return 0;
    }

    //作用：返回listview的iotem显示的控件
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View view = null;

        //第一步的作用是用来获取listView上的item的View对象，目的是set数据进到该itemView里面
        //1.复用converView优化listView，创建一个view作为getView（）的返回值用来显示一个条目
        if (convertView == null){
            view = LayoutInflater.from(context).inflate(R.layout.news_description_item_layout, null);
            Log.d(TAG, "getView: " + view);
        }else {
            view = convertView;
            Log.d(TAG, "getView000: " + view);
        }

        //2.获取View上面的子控件
        ImageView icon = (ImageView) view.findViewById(R.id.news_description_item_layout_imageView);
        TextView title = (TextView) view.findViewById(R.id.news_description_item_layout_title);
        TextView context = (TextView) view.findViewById(R.id.news_description_item_layout_context);

        //3.获取对应position上面的bean数据
        AllNewsBean allNewsBean = arrayList.get(position);

        //4.把对应bean的数据set进对应position上面的View;
        icon.setImageDrawable(allNewsBean.icon);
        title.setText(allNewsBean.title);
        context.setText(allNewsBean.context);


        return view;
    }
}
```



