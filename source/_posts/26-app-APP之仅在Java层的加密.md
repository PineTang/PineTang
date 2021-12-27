---
title: 26 app APP之仅在Java层的加密
date: 2021-12-27 11:00:05
tags: [App逆向]
categories: 反爬
---

## 定位并抠出加密的 Java 代码,稍微调整后
```java
// package OooO0O0.OooO.OooO00o.OooOOo;

import java.io.UnsupportedEncodingException;
import java.util.ArrayList;

/* compiled from: proguard-dict.txt */
public class OooOo {
    public static int OooO00o(int i, int i2, int i3) {
        return ((i ^ -1) & i3) | (i2 & i);
    }

    public static int OooO0O0(int i, int i2, int i3, int i4, int i5, int i6) {
        return OooO0oo((i + OooO00o(i2, i3, i4)) + i5, i6);
    }

    public static int OooO0OO(int i, int i2, int i3) {
        return ((i & i3) | (i & i2)) | (i2 & i3);
    }

    public static int OooO0Oo(int i, int i2, int i3, int i4, int i5, int i6) {
        return OooO0oo(((i + OooO0OO(i2, i3, i4)) + i5) + 1518500249, i6);
    }

    public static int OooO0o(int i, int i2, int i3, int i4, int i5, int i6) {
        return OooO0oo(((i + OooO0o0(i2, i3, i4)) + i5) + 1859775393, i6);
    }

    public static int OooO0o0(int i, int i2, int i3) {
        return (i ^ i2) ^ i3;
    }

    public static int OooO0oo(int i, int i2) {
        return (i >>> (32 - i2)) | (i << i2);
    }

//    public String OooO(byte[] bArr){
    public String OooO(String page, String currentTimeMillis) throws UnsupportedEncodingException {
        StringBuilder stringBuilder = new StringBuilder();
        stringBuilder.append("page="+page);
//        long currentTimeMillis = System.currentTimeMillis();
        stringBuilder.append(currentTimeMillis);
        byte[] bArr = stringBuilder.toString().getBytes("UTF-8");

        ArrayList OooO0oO = OooO0oO(bArr);
        int i = 1732584193;
        int i2 = -271733879;
        int i3 = -1732584194;
        int i4 = 271733878;
        for (int i5 = 0; i5 < OooO0oO.size() / 64; i5++) {
            int i6;
            int i7;
            int i8;
            int OooO0O0;
            int i9;
            int[] iArr = new int[16];
            for (i6 = 0; i6 < 16; i6++) {
                i7 = (i5 * 64) + (i6 * 4);
                iArr[i6] = (((Integer) OooO0oO.get(i7 + 3)).intValue() << 24) | ((((Integer) OooO0oO.get(i7)).intValue() | (((Integer) OooO0oO.get(i7 + 1)).intValue() << 8)) | (((Integer) OooO0oO.get(i7 + 2)).intValue() << 16));
            }
            int[] iArr2 = new int[]{0, 4, 8, 12};
            i7 = i;
            int i10 = i2;
            int i11 = i3;
            int i12 = i4;
            i6 = 0;
            while (i6 < 4) {
                i8 = iArr2[i6];
                i7 = OooO0O0(i7, i10, i11, i12, iArr[i8], 3);
                OooO0O0 = OooO0O0(i12, i7, i10, i11, iArr[i8 + 1], 7);
                i11 = OooO0O0(i11, OooO0O0, i7, i10, iArr[i8 + 2], 11);
                i10 = OooO0O0(i10, i11, OooO0O0, i7, iArr[i8 + 3], 19);
                i6++;
                i12 = OooO0O0;
            }
            iArr2 = new int[]{0, 1, 2, 3};
            i8 = i7;
            i6 = i12;
            for (i9 = 0; i9 < 4; i9++) {
                i12 = iArr2[i9];
                i8 = OooO0Oo(i8, i10, i11, i6, iArr[i12], 3);
                i6 = OooO0Oo(i6, i8, i10, i11, iArr[i12 + 4], 5);
                i11 = OooO0Oo(i11, i6, i8, i10, iArr[i12 + 8], 9);
                i10 = OooO0Oo(i10, i11, i6, i8, iArr[i12 + 12], 13);
            }
            iArr2 = new int[]{0, 2, 1, 3};
            i7 = i8;
            i9 = 0;
            while (i9 < 4) {
                i8 = iArr2[i9];
                OooO0O0 = OooO0o(i7, i10, i11, i6, iArr[i8], 3);
                i6 = OooO0o(i6, OooO0O0, i10, i11, iArr[i8 + 8], 9);
                i11 = OooO0o(i11, i6, OooO0O0, i10, iArr[i8 + 4], 11);
                i10 = OooO0o(i10, i11, i6, OooO0O0, iArr[i8 + 12], 15);
                i9++;
                i7 = OooO0O0;
            }
            i += i7;
            i2 += i10;
            i3 += i11;
            i4 += i6;
        }
        return String.format("%02x%02x%02x%02x", new Object[]{Integer.valueOf(i), Integer.valueOf(i2), Integer.valueOf(i3), Integer.valueOf(i4)});
    }

    public final ArrayList<Integer> OooO0oO(byte[] bArr) {
        int length = bArr.length * 8;
        ArrayList arrayList = new ArrayList();
        int i = 0;
        for (byte valueOf : bArr) {
            arrayList.add(Integer.valueOf(valueOf));
        }
        arrayList.add(Integer.valueOf(128));
        while (((arrayList.size() * 8) + 64) % 512 != 0) {
            arrayList.add(Integer.valueOf(0));
        }
        while (i < 8) {
            arrayList.add(Integer.valueOf((int) ((((long) length) >>> (i * 8)) & 255)));
            i++;
        }
        return arrayList;
    }

    public static void main(String[] args){
       OooOo signcls = new OooOo();
       long currentTimeMillis = System.currentTimeMillis();
       System.out.println(currentTimeMillis);
       try {
                                    //page  time
           String sign = signcls.OooO("18", currentTimeMillis);
           System.out.println(sign);
       } catch (UnsupportedEncodingException e) {
           e.printStackTrace();
       }
    }
}

```


## 使用 python 调用打包好的 jar 包
```python
#%%
# 引入jpype模块
import jpype
import os
import time
import httpx
from jpype import *

# # 加载刚才打包的jar文件
jarpath = os.path.join(os.path.abspath("."), "/spider26.jar")

# # 获取jvm.dll 的文件路径
jvmPath = jpype.getDefaultJVMPath()

# # 开启jvm
jpype.startJVM(jvmPath,"-ea", "-Djava.class.path=%s" % (jarpath))

test = JPackage("com.spider.sign26").OooOo()

def req(page,sign,t):
    headers = {
        'Host': 'www.python-spider.com',
        'accept-language': 'zh-CN,zh;q=0.8',
        'user-agent': 'Mozilla/5.0 (Linux; U; Android 10; zh-cn; M2007J1SC Build/QKQ1.200419.002) AppleWebKit/533.1 (KHTML, like Gecko) Version/5.0 Mobile Safari/533.1',
        'content-type': 'application/x-www-form-urlencoded',
        'accept-encoding': 'gzip',
        'cache-control': 'no-cache',
    }
    data = {
        'page': page,
        'sign': sign,
        't': t
    }
    with httpx.Client(headers=headers, http2=True) as client:
        response = client.post('https://www.python-spider.com/api/app1', headers=headers, data=data)
        return response

def fomatNum(datas):
    for data in datas['data']:
        yield int(data['value'].strip())

if __name__ == '__main__':
    val_list = []
    for i in range(100):
        print("page: ", i+1)
        sing_time = str(int(time.time()*1000))
        signcls = test.OooO(str(i+1), sing_time)
        rsp = req(str(i+1),signcls,sing_time)
        print(rsp.json())
        for val in fomatNum(rsp.json()):
            val_list.append(val)
            print(val)
    print(sum(val_list))

    # 关闭
    jpype.shutdownJVM()

# %%


```