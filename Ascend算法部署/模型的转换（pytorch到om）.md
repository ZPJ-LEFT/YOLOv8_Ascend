# Ascend模型转换（pytorch到om）

![ModelTrans](./Images/ModelTrans.png)

## pytorch -> onnx

在pytorch框架下，利用torch.onnx库，将模型转化为onnx格式，步骤如下：
1. 加载pytorch模型
2. 定义pytorch模型的输入维度，例如(1,3,256,256)
3. 调用torch.onnx.export()进行转换

例如，将resnet18转化为onnx格式：
```
import torch
import torch.onnx
import torchvision.models as models
# 加载pytorch模型
model = models.resnet18(pretrained=Ture) 
model.eval()
# 设置模型输入
dummy_input = torch.randn(1,3,256,256, device = 'cpu')
# 模型转化
name = 'resnet18.onnx'
torch.onnx.export(model, dummy_input, name, export_params=True)
```

## [onnx -> om](https://www.hiascend.com/document/detail/zh/Atlas200IDKA2DeveloperKit/23.0.RC2/Application%20Development%20Guide/tmuacop/tmuacop_0028.html)

基于ONNX模型，需要在Ubuntu 22.04系统下利用ATC（Ascend Tensor Compiler）工具将开源框架的网络模型转换为适配昇腾AI处理器的离线模型（*.om文件）。

ATC工具包含在CANN开发套件中，因此实际上是在安装CANN开发套件，安装过程如下：

1. [确认安装所有依赖](https://www.hiascend.com/document/detail/zh/canncommercial/700/envdeployment/instg/instg_0028.html)。
2. 下载并安装CANN开发套件包“Ascend-cann-toolkit_7.0.RC1_linux-x86_64.run”。
3. 配置环境变量。

> [!TIP]
> 也可以直接在Ascend开发板上执行ATC工具，因为Ascend开发板已经安装好CANN环境。

执行以下指令生成离线模型：

```
atc --model=model.onnx --framework=5 --output=model --soc_version=Ascend310B4
```

当输出`ATC run success`时证明模型转换成功。
