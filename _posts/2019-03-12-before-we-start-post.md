---
layout: post
title:  "0：Before We Start"
date:   2019-03-12
excerpt: "Tools we need for shader learning."
tag:
- Shader
- Unity
---

# 需要做的准备

​	学习任何事物都离不开趁手的工具，Shader也不例外。我们可以选择Sublime Text，JB Rider Free For Student，甚至VS作为我们的编辑器，但试用之后还是觉得VSCode是比较优的选择。下面我们将基于VSCode搭建一个Shaderlab编辑器。（编辑器不带调试功能，我们仍需要借助Unity进行调试。）

​	首先我们直接从VSCode官网下载VSCode Free ver。打开VSCode，选择Extensions--Search “Shaderlab”--Install “ShaderlabVSCode”。

​	接着我们配置Shaderlab的json文件。上方菜单：File--Preferences--User Snippets--Search “shaderlab”--选择shader.json进入。

```C
	// Place your snippets for shaderlab here. Each snippet is defined under a snippet name and has a prefix, body and 
	// description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
	// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the 
	// same ids are connected.
	// Example:
	// "Print to console": {
	// 	"prefix": "log",
	// 	"body": [
	// 		"console.log('$1');",
	// 		"$2"
	// 	],
	// 	"description": "Log output to console"
	// }
```

​	我们可以看到如上的文档说明，大意是：

​	在这里你可以定制你的shaderlab语法模板。用 **{}** 包围整个代码块，每一个模板用 **,** 分隔开。

​	对于每一个模板，内部每一个属性也用 **,** 分隔开。首先为模板起一个名字，对应 **"Print to console"** ,接着我们声明它的 **prefix** 属性，这是用于匹配你的模板的检索标志，对应 **“log”** ，即我们输入log时，匹配栏会出现这个模板。

​	接着编辑 **“body”** 部分，这是模板的内容，格式为["string",]，在[]内部，每一个字符串自成一行，可以用转义字符实现缩进换行，若想实现高级定制，则可插入 **${number:hint}** 对于每一个 **$** ，会按照 **number** 从小到大的顺序，给出 **hint** 提示补全模板，使用 <kbd>Tab</kbd> 切换至下一个可编辑的单元，最终光标会停留在 **$0** 处。

​	基于这些，我们便可以灵活的配置自己所需的模板。下面也给出一份我自己常用的模板配置，这份模板也会不定期更新。

```json
{
	"Shaderlab": {
		"prefix": "shaderlab",
		"body": [
			"Shader \"${1:Shader Name}\" {\n",

				"\tProperties {",
					"\t\t_MainTex (\"Base (RGB)\", 2D) = \"white\" {}",
					"\t\t_Color (\"Color\", Color) = (1, 1, 1, 1)",
				"\t}\n",

				"\tSubShader {",
					"\t\tCGINCLUDE",
					"\t\t#include \"UnityCG.cginc\"\n",
			
					"\t\tsampler2D _MainTex;",
					"\t\tfloat4 _MainTex_ST;\n",
					
					"\t\tstruct a2v {",
						"\t\t\tfloat4 vertex : POSITION;",
						"\t\t\tfloat2 texcoord : TEXCOORD0;",
					"\t\t};\n",
					
					"\t\tstruct v2f {",
						"\t\t\tfloat4 pos : SV_POSITION;",
						"\t\t\tfloat2 uv : TEXCOORD0;",
					"\t\t};\n",
					
					"\t\tv2f vert(a2v v) {",
						"\t\t\tv2f o;",
						"\t\t\to.pos = UnityObjectToClipPos(v.vertex);",
						"\t\t\to.uv = TRANSFORM_TEX(v.texcoord, _MainTex);",
						"\t\t\treturn o;",
					"\t\t}\n",
					
					"\t\tfixed4 frag(v2f i) : SV_Target {",
						"\t\t\tfixed4 color;",
						"\t\t\tcolor = tex2D(_MainTex, i.uv);",
						"\t\t\treturn color;",
					"\t\t}",
					"\t\tENDCG\n",
			
					"\t\tPass {\n",

						"\t\t\tCGPROGRAM",
						"\t\t\t#pragma vertex vert",
						"\t\t\t#pragma fragment frag",
						"\t\t\tENDCG",
					"\t\t}",
				"\t}\n",

				"\tFallBack \"Diffuse\"",
			"}"
		],
		"description": "Shaderlab Template"
	},
	"Range()": {
		"prefix": "range",
		"body": [
			"Range(${1:left}, ${2:right})"
		],
		"description": "Range for properties"
	},
	"Pass": {
		"prefix": "pass",
		"body": [
			"Pass {",
				"\t$0",
			"}"
		],
		"description": "Pass for Shaderlab Subshader"
	},
	"Tags": {
		"prefix": "tags",
		"body": [
			"Tags {",
				"\t$0",
			"}"
		],
		"description": "Tags in Pass"
	}
}
```

