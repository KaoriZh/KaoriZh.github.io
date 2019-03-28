---
layout: post
title:  "1.2：Properties"
date:   2019-03-28
excerpt: "Properties in Shaderlab and how them works in and between shaders and scripts."
tag:
- Shader
- Unity
- Properties
---

上一节我们了解了一个Shader的基本结构，这一节，我们从 **Properties** 代码块入手，了解Shaderlab的参数组成。

# 如何声明Properties

## 常规属性

​	Properties的属性常用的主要有以下几类： **Numbers** （数值）、 **Colors**（颜色） 、 **Vectors** （向量）、 **Textures** （纹理），其中，对于数值属性，还提供了Range（）设置区间值限定数值范围（注意，属性中没有 **Matrices** （矩阵））。下面给出一些Shader的属性定义方法，具体的属性值以及默认参数可以参考Unity官方文档ShaderLab: Properties（unity3D/Editor/Data/en/Manual/SL-Properties.html）。

```
//定义Numbers（数值）和Sliders（区间值）
name ("display name", Range (min, max)) = number
name ("display name", Float) = number
name ("display name", Int) = number

//定义Colors（颜色）和Vectors（向量）
//颜色表示为一个四维向量，对应颜色的RGBA通道；每个分量取值为[0,1]
//向量必须是四维向量
name ("display name", Color) = (number,number,number,number)
name ("display name", Vector) = (number,number,number,number)

//定义Textures（纹理）
name ("display name", 2D) = "defaulttexture" {}
name ("display name", Cube) = "defaulttexture" {}
name ("display name", 3D) = "defaulttexture" {}
```



## Texture（纹理）

​	Unity给出了纹理声明时可选的默认默认值：

### 对于 2D Texture（2维纹理）

|  内置默认值  |     (R,G,B,A)      |
| :----------: | :----------------: |
| empty string |        null        |
|   "white"    |     1, 1, 1, 1     |
|   "black"    |     0, 0, 0, 0     |
|    "gray"    | 0.5, 0.5, 0.5, 0.5 |
|    "bump"    |  0.5, 0.5, 1, 0.5  |
|    "red"     |     1, 0, 0, 0     |

### 对于Non 2D Texture （非2维纹理，如：Cube， 3D， 2DArray）

​	默认值是empty string，当他们没有材质指定时，默认使用**“gray”**

​	Unity文档还给出了属性的命名规范：

> Each property inside the **shader** 
>  is referenced by **name** (in Unity, it’s common to start shader property names with underscore).

​	即名称以下划线开头，首字母大写驼峰命名。

​	此外，一般纹理都会包含  **tiling & offset**（缩放和偏移），这会被作为一个 **float4** 类型的变量传入cg代码块中，这个变量有一个特别的名称： **{TextureName}_ST** ；同样，纹理还有一个特殊的属性 **Texture size** （纹理尺寸），这个变量也有一个特别的名称：**{TextureName}_TexelSize**  ，它保存了纹理的尺寸信息。下面给出一个例子：

```
Shader "ShaderName" {
    Properties {
    	_MainTex ("Albedo", 2D) = "white" {}
	}
	SubShader {
        Pass {
            CGPROGRAM
            
            sampler2D _MainTex;
            float4 _MainTex_ST;
            // 其中x,y分量是缩放比，z,w分量是偏移量
            float4 _MainTex_TexelSize;
            // 其中：
            // x/y的值为纹理宽度/高度的倒数
       	    // z/w的值为纹理宽度/高度
            
            ENDCG
        }
	}
}
```

​	还有一种高品质纹理Texture HDR，这里不做展开。

### Attributes（属性）

​	Unity还将C#的Attributes（属性）特性引入Shaderlab。在每个属性之前，我们可以指定一些由方括号包围的标签来具体说明这个属性的显示方式，下面给出几个build-in的标签：

- [Header(text)]：会在这个标签上方生成一个文本

- [Toggle]：这个属性会作为一个勾选项

- [HideInInspector]：不在Inspector面板显示这个属性

- [NoScaleOffset]：不显示纹理的缩放/偏移编辑器

- [Normal]：提示这是一个法线纹理

  标签的详细用法可以参阅官方文档（unity3D/Editor/Data/en/ScriptReference/MaterialPropertyDrawer.html）。

  我们还可以在脚本中重写**OnGUI**方法定制自己的标签。

# Properties怎样提供给Shader

​	一般来说，属性从3个渠道获得：

- 在 **MaterialPropertyBlock** 中设置的Per-Renderer values（逐渲染器值），这通常称为“per-instance”数据

- 在指定的已渲染对象的 **Material** 面板设置的值

- 由Unity的内置渲染或自己的脚本设置的全局Shader属性

  这三个属性的优先级如下：

  per-instance数据优先级最高，然后是材质自身的属性，最后是全局属性，如果三种属性都没有设置，那么Unity会使用默认值



​	**定义Shader属性的目的在于更便捷地使用，下面我们来看看如何在Shaderlab以及Script中使用这些属性。**

# Properties和Shader

### 	我们可以在CGPROGRAM（CG代码块）中声明同名变量和Shader进行交互

​	Shaderlab支持插入**Cg/HLSL**和**GLSL**（不推荐），在以后的学习中，我们以Cg为主。

​	在 **Properties** 声明的参数会作为 **Material** （材质）的数据按顺序显示在材质面板。

```
Shader "ShaderName" {
    Properties {
        _Texture ("Texture", 2D) = "white" {}
        _Color ("Color", Color) = (1, 1, 1, 1)
        _Vector ("Vector", Vector) = (1, 1, 1 ,1)
        _Cubemap ("Cubemap", CUBE) = "" {}
    }
    SubShader {
        Pass {
            CGPROGRAM
            //颜色和向量用float4 / half4 / fixed4存储，精度从高到低
            //数值用float / half / fixed存储，精度从高到低
            //2D纹理 / 3D纹理 / Cubemap分别用sampler2D / 3D / CUBE存储
            sampler2D _Texture;
            float4 _Texture_ST;
            float4 _Texture_TexelSize;
            fixed4 _Color;
            float4 _Vector;
            samplerCUBE _Cubemap;
            ENDCG
        }
    }
}
```

​	此外，我们还可以声明一些变量供Shader的 **fixed function** 使用，使用时用方括号包围。例如：我们可以通过这种方式设置混合系数（混合系数等标签我们会在以后的章节进行学习）：

```
Shader "ShaderName" {
    Properties {
 	   //...
        _SrcBlend ("SrcBlend", Int) = 1
        _DstBlend ("DstBlend", Int) = 0
    }
    SubShader {
        //...
        Pass {
            Blend [_SrcBlend] [_DstBlend]
        }
        //...
    }
}
```

# Properties和Script

​	我们可以通过 **Material** 的 **Set** 方法修改Properties以实现最基础的交互。

```c#
using UnityEngine;

public class TestClass : MonoBehaviour {
	
	private Material testMaterial = null;

	public Material material {
		get {
			return testMaterial;
		}
	}

    void Start () {
        //把这个材质的shader中的name属性值设为1.0
        testMaterial.setFloat("name", 1.0);
    }
}
```

# 最后

​	这里我给出一份属性的示例：

```
Shader "MyShaders/ShaderForProperties" {

    Properties {
        [NoScaleOffset] _Texture ("Texture", 2D) = "white" {}
        _Color ("Color", Color) = (1, 1, 1, 1)
        [HideInInspector] _Vector ("Vector", Vector) = (1, 1, 1 ,1)
        _Cubemap ("Cubemap", CUBE) = "" {}
        [Header(Blend Parameter)] _SrcBlend ("SrcBlend", Int) = 1
        _DstBlend ("DstBlend", Int) = 0
    }

    SubShader {
        
        Pass {
            Blend [_SrcBlend] [_DstBlend]

            CGPROGRAM

            sampler2D _Texture;
            float4 _Texture_ST;
            float4 _Texture_TexelSize;
            fixed4 _Color;
            float4 _Vector;
            samplerCUBE _Cubemap;

            ENDCG
        }

    }

    FallBack "Diffuse"
}
```

​	下面是对应的属性面板（需要把Shader添加到材质内才会显示属性面板）：

![InspectorOfProperties](D:\programming\KaoriZh.github.io\_Images\InspectorOfProperties.png)