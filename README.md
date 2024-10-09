**阅读目录**

* [前言](#_label0)
* [项目介绍](#_label1)
* [项目功能](#_label2)
* [项目效果](#_label3):[蓝猫机场](https://fenfang.org)
* [项目地址](#_label4)
* [最后](#_label5)

## 前言


JIEJIE.NET是一款强大的开源.NET程序集混淆工具。它利用深度加密技术和多样化的混淆策略，有效地保护了.NET软件的版权和源代码安全，防止未经授权的访问和篡改。


## 项目介绍


JIEJIE.NET是一个用C\#开发的开源.NET代码加密工具。许多开发人员担忧其软件会被破解，版权遭到侵犯，市场上虽有如PreEmptive dotfuscator等工具可用，但在某些特定需求上可能无法完全满足。


因此JIEJIE.NET应运而生，它能够对.NET程序集进行深度加密，从而有效地保护软件版权。尤为值得一提的是，这款工具是完全开源的，可供大家免费使用。


## 项目功能


**1、名称混淆**


重命名类型和成员：通过变更类型和成员的名称，增加理解API语义的难度。


**2、控制流混淆**


混淆控制流：随机化IL代码的控制流，破坏原有的语法结构，使代码变得难以解读。


**3、字符串加密**


加密字符串值：搜集程序集中定义的所有字符串值，将其转换为静态只读字段，并对这些值进行加密处理。


**4、资源保护**


加密资源文件：加密并隐藏嵌入式资源文件（如.resources文件），使其不易被篡改。


**5、分配调用栈保护**


隐藏分配调用栈：调整关键字符串值的分配调用流程，防止黑客利用内存分析工具搜寻关键信息。


![](https://img2024.cnblogs.com/blog/576536/202410/576536-20241009105246293-515087253.png)


## 项目效果


### 1、加密程序集


JieJie.NET可以收集程序集中定义的所有字符串值，将它们转换为新类中的静态只读字段，并对这些值进行加密处理。这样就无法直接搜索字符串值，增加了破解的难度。




```
private string GetLicenseMessage()
{
    return "软件的许可授权 :" + Environment.UserName;
}
```


使用JieJie.NET处理后的代码




```
private string GetLicenseMessage()
{
    string text = _0._6 + Environment.UserName;
    return text;
}
//  also create a new class, contains all string value in assembly in random order.
internal static class _0
{
    public static readonly string _0;
    public static readonly string _1;
    public static readonly string _2;
    public static readonly string _3;
    public static readonly string _4;
    public static readonly string _5;
    public static readonly string _6;
    public static readonly string _7;
    public static readonly string _8;
    public static readonly string _9;
    public static readonly string _10;
    public static readonly string _11;
    public static readonly string _12;
    public static readonly string _13;
    public static readonly string _14;
    public static readonly string _15;
    public static readonly string _16;
    public static readonly string _17;
    public static readonly string _18;
    public static readonly string _19;
    public static readonly string _20;
    public static readonly string _21;
​
    static _0()
    {
        byte[] datas = _BytesContainer__._0();
        _11 = GetStringByLong(datas, 151732605047602L);
        _20 = GetStringByLong(datas, 450799767951810L);
        _7 = GetStringByLong(datas, 101155071172227L);
        _4 = GetStringByLong(datas, 47279000500949L);
        _15 = GetStringByLong(datas, 415615395474299L);
        _5 = GetStringByLong(datas, 54975582493063L);
        _2 = GetStringByLong(datas, 17592187197342L);
        _14 = GetStringByLong(datas, 206708198516324L);
        _8 = GetStringByLong(datas, 124244814685054L);
        _21 = GetStringByLong(datas, 459595860893446L);
        _6 = GetStringByLong(datas, 72567769190975L);
        _13 = GetStringByLong(datas, 182518931688172L);
        _18 = GetStringByLong(datas, 433207581847376L);
        _16 = GetStringByLong(datas, 417814419099513L);
        _3 = GetStringByLong(datas, 36283884381871L);
        _1 = GetStringByLong(datas, 9895605165436L);
        _9 = GetStringByLong(datas, 136339442622330L);
        _19 = GetStringByLong(datas, 440904163377248L);
        _17 = GetStringByLong(datas, 426610511995160L);
        _0 = GetStringByLong(datas, 598562L);
        _10 = GetStringByLong(datas, 148434069970387L);
        _12 = GetStringByLong(datas, 158329675868829L);
    }
    private static string GetStringByLong(byte[] datas, long key)
    {
        int num = (int)(key & 0xFFFF) ^ 0xEF83;
        key >>= 16;
        int num2 = (int)(key & 0xFFFFF);
        key >>= 24;
        int num3 = (int)key;
        char[] array = new char[num2];
        int num4 = 0;
        while (num4 < num2)
        {
            int num5 = num4 + num3 << 1;
            array[num4] = (char)(((datas[num5] << 8) + datas[num5 + 1]) ^ num);
            num4++;
            num++;
        }
        return new string(array);
    }
}
```


### 2、混淆类成员的顺序


当我们写一个大型类的代码时，通常情况下，具有相同目的的字段或方法会放置得很近。


例如：




```
private string _RegisterCode = null;
private bool _IsRegisteredFlag = false;
public void SetRegisterCode(string code) { }
public bool IsRegisterdCodeOK(string code) { }
public string GetErrorMessageForRegister();
// 其他成员
```


当黑客捕获到某个关键成员（例如 \_RegisterCode）并分析附近的其他成员时，可能会获取更多有用的信息。 但是，JieJie.NET 可以混淆类成员的顺序，就像这样：




```
private bool _IsRegisteredFlag = false;
// 其他成员
private string _RegisterCode = null;
// 其他成员
public string GetErrorMessageForRegister();
// 其他成员
public bool IsRegisterdCodeOK(string code) { }
// 其他成员
public void SetRegisterCode(string code) { }
// 其他成员
```


这样，附近的其他成员可能与某个关键成员无关，这可以使破解变得更加困难。


### 3、隐藏数组定义


经常在源代码中定义数组，例如：




```
public static byte[] GetBytes()
{
    return new byte[]
    {
        85,
        203,
        85,
        204,
        85,
        255,
        85,
        245,
        85
    };
}
```


使用 JieJie.NET 后的代码




```
public static byte[] GetBytes()
{
    byte[] array = new byte[18];
    InnerAssemblyHelper20211018.MyInitializeArray(
        array, 
        (RuntimeFieldHandle)/*OpCode not supported: LdMemberToken*/);
    return array;
}
```


这样处理后，原始数组的定义被隐藏起来，增加了逆向工程的难度。


### 4、简单使用


.NET框架控制台应用程序。


它支持以下命令行参数：


* `input`：\[必需，默认参数，输入.NET程序集文件的完整路径，可以是.exe或.dll文件，目前仅支持.NET Framework 2\.0及以上版本]
* `output`：\[可选，输出.NET程序集文件的完整路径；如果为空，则使用输入参数的值]
* `snk`：\[可选，强名称签名文件（.snk）的完整路径，用于给输出程序集添加强名称]
* `switch`：\[可选，多开关参数，用逗号分隔，也可以在\[System.Reflection.ObfuscationAttribute.Feature]中定义。支持：


	+ `+controlflow`：启用方法体中的控制流混淆。
	+ `-controlflow`：禁用方法体中的控制流混淆。
	+ `±strings`：启用/禁用字符串值加密。
	+ `±resources`：启用/禁用资源数据加密。
	+ `±memberorder`：启用/禁用类型中成员列表顺序的混淆。
	+ `±rename`：启用/禁用类型或成员名称的重命名。
	+ `±allocationcallstack`：启用/禁用字符串值分配调用栈的加密。]
* `mapxml`：\[可选，保存类/成员旧名称和新名称映射信息的XML格式文件/目录名称。]
* `pause`：\[可选，在处理完成后暂停控制台。]
* `debugmode`：\[可选，允许显示一些调试信息文本。]
* `sdkpath`：\[可选，设置ildasm.exe所在目录的完整路径。]
* `prefixfortyperename`：\[可选，用于类型名称重命名的前缀。]
* `prefixformemberrename`：\[可选，用于类型成员名称重命名的前缀。]
* `deletetempfile`：\[可选，在任务完成后删除模板文件，默认为false。]
* `merge`：\[可选，合并到结果文件的一些.NET程序集文件。'\*'表示合并所有引用的程序集文件。]
* `.subsystem`：\[可选，整数值，'2'表示GUI模式的应用程序，'3'表示控制台模式的应用程序。]
* `.corflags`：\[可选，整数标志，'3'表示32位进程无强名称签名，'1'表示64位无强名称，'9'表示32位带强名称，'10'表示64位带强名称。]
* `[BlazorWebAssembly]`：\[可选，处理Blazor WebAssembly程序集。]
* `DeadCode`：\[可选，可以是`Disabled`、`Normal`（所有方法重命名，并且没有任何自定义属性且从未使用）或`All`（所有方法重命名且从未使用）。]
* `RemoveCustomAttributeTypeFullnames`：\[可选，要移除的自定义属性类型的完整类型名称列表。]


示例 1：保护 d:\\a.dll 文件，这将修改dll文件。




```
>JIEJIE.NET.exe d:\a.dll
```


示例 2：分析 d:\\a.dll 文件，并将结果写入另一个带有强名称的dll文件。启用控制流混淆，但不加密资源。




```
>JIEJIE.NET.exe input=d:\a.dll output=d:\publish\a.dll snk=d:\source\company.snk switch=+controlflow,-resources
```


以上是工具的功能和使用方法。


## 项目地址


GitHub：[https://github.com/dcsoft\-yyf/JIEJIE.NET](https://github.com)


## 最后


如果你觉得这篇文章对你有帮助，不妨点个赞支持一下！你的支持是我继续分享知识的动力。如果有任何疑问或需要进一步的帮助，欢迎随时留言。


也可以加入微信公众号**\[DotNet技术匠]** 社区，与其他热爱技术的同行一起交流心得，共同成长！**优秀是一种习惯，欢迎大家留言学习！**


![](https://img2024.cnblogs.com/blog/576536/202408/576536-20240814113403514-910171896.png)


