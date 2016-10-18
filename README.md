# InitializeConverter
This is a constructor for initializing your model(bean) class. 


Its best use is to avoid handmade model class initialization for testing especially when your model class is so complex like this:(这是一个用于类的默认初始化构造器，主要用于客户端完成功能逻辑与页面布局后，绕过网络请求的接口进行自测。如果model或bean类比较复杂，比如)

```
public class TestModel {
  public int id;
  public String a;
  public String b;
  public String c;
  public String d;
  public InlineModel e;
  public List<InlineModel> f;
}

public static class InlineModel {
  public int id;
  public String h;
  public String g;
}
```

So we have to do a lot of work to initialize the TestModel:(那么我们不得不写一大堆代码来实例化我们的类以及为它的变量赋值以进行测试)

```
    TestModel model = new TestModel();
    model.id = 0;
    model.a = "cds";
    model.b = "cds";
    model.c = "cds";
    model.d = "cds";
    
    model.e = new InlineModel();
    model.e.g = "ddd";
    model.e.h = "aaa";
    model.e.id = 0;
    
    model.f = new ArrayList<>();
    InlineModel inlineModel = new InlineModel();
    inlineModel.g = "ddd";
    inlineModel.h = "aaa";
    inlineModel.id = 0;
    model.f.add(inlineModel);
    //now we are done
```

If you don't care much about the test data, you can use InitializeConverter instead:(如果你并不在乎你赋的值是什么而仅仅是想调试你的功能逻辑以及界面布局的话，那么你就可以用它了)
```
    TestModel model = (TestModel) new InitializeConverter.InitializeConverterBuilder().create(
        BuildConfig.BASE_PACKAGE_NAME, TestModel.class);
```

If you do care about the test data, you can use InitializeConverter this way.: (如果你在乎你赋的值是什么的话，那么目前只支持针对类型进行赋初值，否则的话与手写初始化就差不多了)

```
    
    
    TestModel model = (TestModel) new InitializeConverter.InitializeConverterBuilder().s(
        "String type initialization")
        .i(200)
        .f(4000.22f)
        .d(4000.22)
        .create(BuildConfig.BASE_PACKAGE_NAME, TestModel.class);
```

#Note
* InitializeConverter doesn't support non-static inline class, So try to avoid it.（不支持非静态内部类）
* It's mainly used for testing because of the awkward performance of reflect operation.(它仅用于测试，因为赋值操作都是反射做到的)
* PackageName will be needed for recognize the custom class. By default, context.getPackageName() will be fine.But if applicationId is modified by gradle productFlavors, then you have to set the original one in BuildConfig.(包名用于识别自定义类，默认情况下直接使用context.getPackageName()传入就可以了，但是如果productFlavors修改了包名的话就需要自己设置项目的基本包名，就像上面示例所写)

