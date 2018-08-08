---
title: Android一次申请多个动态权限
date: 2018-07-03 00:00:00
tag: [Android,note]
---
# Android一次申请多个动态权限
今天做一个短信群发软件，需要申请多个权限，遇到点小问题。
## 在 Android6.0（Api 23）以下的版本，申请权限。
- 只要在Manifest文件中添加权限：
```java
//读写手机状态和身份权限 及 短信权限
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.SEND_SMS" />
```
## Android在6.0以后就要使用动态权限了，否者程序可能无法进行某些功能操作。 
- Android 6.0对应的Android SDK等级是23.所以一般是先判断手机的版本是否是6.0以上再进行动态请求权限。
- 动态申请权限如下:
```java
if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.READ_PHONE_STATE)
        != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(MainActivity.this, new String[]{Manifest.permission.READ_PHONE_STATE}, MY_PERMISSIONS_REQUEST_READ_PHONE_STATE);
} else {
    //TODU
}
```
- 其中MY_PERMISSIONS_REQUEST_READ_PHONE_STATE 是自定义的类常量，可以像下面这样在activity中定义：

```java
public final static int MY_PERMISSIONS_REQUEST_READ_PHONE_STATE = 1;
```
## 一个请求读写手机状态和身份权限和短信权限的实例
```java
package com.example.xiao.sms;

import android.Manifest;
import android.app.PendingIntent;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.support.v4.app.ActivityCompat;
import android.support.v4.content.ContextCompat;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.telephony.SmsManager;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {
    private EditText phoneNum;
    private EditText message;
    private Button sendSMS;
    private static final int MY_PERMISSIONS_REQUEST_SEND_SMS = 1;
    private static final int MY_PERMISSIONS_REQUEST_READ_PHONE_STATE = 1;
    String[] permissions = new String[]{Manifest.permission.READ_PHONE_STATE,
            Manifest.permission.SEND_SMS};
    List<String> mPermissionList = new ArrayList<>();
    private final int mRequestCode = 100;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        phoneNum = (EditText)findViewById(R.id.phone_num);
        message = (EditText)findViewById(R.id.message);
        sendSMS = (Button) findViewById(R.id.send_message);
        initPermission();
        sendSMS.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.SEND_SMS)
                        != PackageManager.PERMISSION_GRANTED) {
                    ActivityCompat.requestPermissions(MainActivity.this, new String[]{Manifest.permission.SEND_SMS}, MY_PERMISSIONS_REQUEST_SEND_SMS);
                } else if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.READ_PHONE_STATE)
                        != PackageManager.PERMISSION_GRANTED) {
                    ActivityCompat.requestPermissions(MainActivity.this, new String[]{Manifest.permission.READ_PHONE_STATE}, MY_PERMISSIONS_REQUEST_READ_PHONE_STATE);
                } else {
                    String[] everyNumber = phoneNum.getText().toString().split("，");
                    String content = message.getText().toString();
                    for (String anEveryNumber : everyNumber) {
                        sendMessage(anEveryNumber, content);
                    }
                    Toast.makeText(MainActivity.this, "发送成功！", Toast.LENGTH_LONG).show();
                }
            }
        });
    }

    public void sendMessage(String phoneNumber,String content){
        SmsManager sms = SmsManager.getDefault();
        ArrayList<String> mSMSMessage = sms.divideMessage(content);
        int messageCount = mSMSMessage.size();
        android.telephony.SmsManager smsManager = android.telephony.SmsManager.getDefault();
        Intent itSend = new Intent("demo_sms_send_action");
        itSend.putExtra("phone_num", phoneNumber);
        ArrayList<PendingIntent> sentIntents = new ArrayList<PendingIntent>(messageCount);
        smsManager.sendMultipartTextMessage(phoneNumber, null, mSMSMessage, sentIntents, null);
    }
    private void initPermission() {
        mPermissionList.clear();

        for (String permission : permissions) {
            if (ContextCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
                mPermissionList.add(permission);
            }
        }

        if (mPermissionList.size() > 0) {
            ActivityCompat.requestPermissions(this, permissions, mRequestCode);
        }else{
            Toast.makeText(MainActivity.this, "已授权！", Toast.LENGTH_LONG).show();
        }
    }
}
```
示例代码根据实际情况做简单修改就可以用在各种动态权限下。
**说明：**上面的权限数组可以请求多个权限，也可以只请求一个权限

# 参考
- [Android一次申请多个动态权限](https://blog.csdn.net/wenzhi20102321/article/details/80487975)