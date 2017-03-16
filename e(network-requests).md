#### Android网络编程-Okhttp2.x

Okhttp2.x处理了很多网络疑难杂症：会从很多常用的连接问题中自动恢复。如果您的服务器配置了多个IP地址，当第一个IP连接失败的时候，OkHttp会自动尝试下一个IP，此外OkHttp还处理了代理服务器问题和SSL握手失败问题。

使用步骤：

1. 配置gradle
2. 创建okHttpClient\(\)对象
3. 以创建者模式创建Request
4. okHttpClient\(\)对象开启newCall\(request）
5. call添加到队列

```java
        //[1.0]创建okhttpClient对象
        OkHttpClient okHttpClient = new OkHttpClient();
        //[2.0]创建Request.Builder()对象
        Request request = new Request.Builder()
                .url("http://blog.csdn.net/itachi85/article/details/51142486")
                .build();
        //[3.0]开始请求
        Call call = okHttpClient.newCall(request);
        //[4.0]把请求添加到队列
        call.enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                Toast.makeText(VolleyActivity.this, "网络请求失败", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {

                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(VolleyActivity.this, "请求成功", Toast.LENGTH_SHORT).show();
                    }
                });
                /*mString = response.body().toString();*/
            }
        });

//        mBtn.setOnClickListener(new View.OnClickListener() {
//            @Override
//            public void onClick(View v) {
//                //这里写实现网络请求成功的逻辑
//                mTv.setText(mString);
//            }
//        });
```



