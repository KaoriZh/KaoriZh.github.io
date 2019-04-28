---

layout: post
title:  "TextField Template"
date:   2019-04-28
excerpt: "TextField Template in FairyGUI."
tag:
- FairyGUI
- TextField
---

记录一个在使用FairyGUI的TextField时遇到的坑。

TextField有一个文本模板功能，可以实现类似占位符的功能，如：`{ number = 0 }`，然后我们可以在脚本中修改number的值：`textField.SetVar("number", "1").FlushVars();`。运行时我们会发现，我们可以成功获取UI下的TextField，但修改操作却无法生效，实在让人费解。

接下来就是漫长的源码测试环节。

```C#
public GTextField SetVar(string name, string value)
{
	if (_templateVars == null)
		_templateVars = new Dictionary<string, string>();
	_templateVars[name] = value;
	return this;
}
```

首先是`SetVar()`，这个只是修改模板信息，通过`Log`输出后排除。那么问题只能是在`FlushVars()`，也就是刷新文本上。

```C#
public void FlushVars()
{
	SetTextFieldText();
	UpdateSize();
}

virtual protected void SetTextFieldText()
{
	string str = _text;
	if (_templateVars != null)
		str = ParseTemplate(str);
	_textField.maxWidth = maxWidth;
	if (_ubbEnabled)
		_textField.htmlText = UBBParser.inst.Parse(XMLUtils.EncodeString(str));
	else
		_textField.text = str;
}
```

观察上面的代码可以发现，文本的转换发生在`ParseTemplate()`，问题极有可能是出在这上面，翻看源码，`ParseTemplate()`其实是一个暴力的字符串处理方法，首先找出所有配对的`{}`，然后获取`_templateVars`中的`key/value`对，去逐个匹配文本中的模板，下面是关键的`key/value`获取部分代码。

```C#
tag = template.Substring(pos1 + 1, pos2 - pos1 - 1);
pos3 = tag.IndexOf('=');
if (pos3 != -1)
{
	if (!_templateVars.TryGetValue(tag.Substring(0, pos3), out value))
		value = tag.Substring(pos3 + 1);
}
else
{
	if (!_templateVars.TryGetValue(tag, out value))
		value = "";
}
```

可以看出，这个key和value匹配是很严格的，不允许出现任何冗余字符，包括空格，或者说，这是一个纯字符串的匹配（可以说是很臭了，空格产生美都不允许）。在我们把模板内的所有空格去掉后，脚本就可以控制number的值了。