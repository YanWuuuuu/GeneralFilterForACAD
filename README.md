# GeneralFilterForACAD

![License](https://img.shields.io/github/license/YanWuuuuu/GeneralFilterForACAD?color=blue)
![AutoCAD](https://img.shields.io/badge/AutoCAD-.NET_API-brightgreen)

AutoCAD 图元通用过滤工具，支持通过实体类型、图层、颜色、块名称等多种方式快速筛选图元。
注意：该项目使用的IFoxCAD较低（现已停止维护），如无需要请使用项目推荐版本；部分低版本AutoCAD不能被VS2022正常调试。

## 功能

- **实体过滤 (EF)**: 根据实体类型（如直线、多段线、标注等，以及部分自定义实体）过滤选择集
- **图层过滤 (XF)**: 根据图层过滤选择集（支持多图层参考）
- **颜色过滤 (CF)**: 根据颜色索引过滤选择集（支持随层颜色）
- **块过滤 (BF)**: 根据块名称过滤选择集（支持多个块参照）

### 编译要求

- .NET Framework 3.5 / 4.0 / 4.5
- AutoCAD .NET API
- IFoxCAD 基础库

### 编译配置

项目支持多目标框架编译：
- **net35**: 适用于 AutoCAD 2010-2012
- **net40**: 适用于 AutoCAD 2013-2014
- **net45**: 适用于 AutoCAD 2015-2024

编译时会根据目标框架自动引用对应的 AutoCAD .NET API 版本。

### 使用方法

1. 编译项目生成 `GeneralFilterForACAD.dll`
2. 将 DLL 文件放入 AutoCAD 的支持文件搜索路径
3. 在 AutoCAD 中加载插件：
   - 方法一：将 DLL 添加到启动组
   - 方法二：使用 `NETLOAD` 命令加载GeneralFilterForACAD.dll
注意：加载插件后，插件所在目录会被添加进AutoCAD 的支持文件搜索路径，因此请勿将插件放在敏感路径（包含个人信息、工作文件）下。

### 命令列表

| 命令 | 功能说明 |
|------|----------|
| `EF` | 实体过滤 - 选择图元进行过滤，参考图元确定过滤类型 |
| `XF` | 图层过滤 - 选择图元进行过滤，参考图元确定目标图层 |
| `CF` | 颜色过滤 - 选择图元进行过滤，参考图元确定目标颜色 |
| `BF` | 块过滤 - 选择图元进行过滤，找到指定块名的块参照 |

### 命令使用示例

项目已经通过自实现算法避免使用CAD的UsePickupSet标识，满足过滤器条件的图元会一直保留直到下一次操作；

操作完成后，符合过滤条件的图元会被自动选中；
可以通过提前选择来进行连续过滤，也可以在上个过滤器滤出的图元上继续过滤。

以 **EF (实体过滤)** 命令为例：

```text
命令: EF
选择图元进行过滤: // 选择需要过滤的图元
选择类型参考图元: // 选择一个或多个参考图元，确定要保留的实体类型
命令: XF ->以图层为条件继续拾取并进行下一次过滤
```

### 一些便利

项目为开发者提供的便利：

- **事务管理**: 自动监听新激活的文档，并且不会重复触发事件订阅
- **自动监听自定义命令**: 利用Linq自动将Command类中为CAD提供的自定义命令添加到监听事件
### 支持的过滤类型

```csharp
public enum FilterType
{
    EntityFilter,   // 实体类型过滤
    ColorFilter,    // 颜色过滤
    LayerFilter,    // 图层过滤
    LinetypeFilter, // 线型过滤（未实现）
    BlockFilter,    // 块过滤
}
```

### 文档管理（待完善）

项目实现了自动文档索引管理，支持多文档环境：

- 自动为每个文档分配唯一索引
- 文档激活时自动清理相关数据

## 项目结构

```
GeneralFilterForACAD/
├── Command.cs           # 命令类，定义用户可调用的过滤命令
├── Init.cs             # 自动加载初始化类
├── GlobalUsings.cs     # 全局 using 引用
├── GeneralFilterForACAD.csproj  # 项目文件
└── README.md           # 项目说明文档
```

### 核心类说明

#### `Core` 类

核心过滤逻辑类，提供静态方法进行图元过滤：

| 方法 | 说明 |
|------|------|
| `GetOrAddIndex(Document)` | 获取或创建文档索引 |
| `GetPath(int)` | 根据索引获取文档路径 |
| `AddEntity(int, ObjectId)` | 向文档的图元列表添加图元 |
| `RemoveDocument(Document)` | 移除文档及其相关数据 |
| `Filter(...)` | 统一过滤接口 |
| `FilterByEntity(...)` | 按实体类型过滤 |
| `FilterByLayer(...)` | 按图层过滤 |
| `FilterByColor(...)` | 按颜色过滤 |
| `FilterByBlock(...)` | 按块名称过滤 |

#### `Command` 类

命令类，提供 AutoCAD 命令接口：

| 命令方法 | 说明 |
|----------|------|
| `EntityFilter()` | 实体过滤命令 |
| `LayerFilter()` | 图层过滤命令 |
| `ColorFilter()` | 颜色过滤命令 |
| `BlockFilter()` | 块过滤命令 |

## 技术栈

- **语言**: C# (.NET Framework)
- **框架**: AutoCAD .NET API
- **依赖**: 
  - IFox.Basal.Source (0.5.2.4)
  - IFox.CAD.Source (0.5.2.4)

## 许可证

本项目采用 [GNU Affero General Public License v3.0](LICENSE.txt) 许可证。

## 作者

[YanWuuuuu](https://github.com/YanWuuuuu)

## 相关链接

- [AutoCAD .NET API 文档](https://help.autodesk.com/view/OARX/2024/ENU/?guid=NET)
- [IFoxCAD 项目](https://github.com/ifoxcompany/IFoxCAD)