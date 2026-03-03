# GeneralFilterForACAD

![License](https://img.shields.io/github/license/YanWuuuuu/GeneralFilterForACAD?color=blue)
![AutoCAD](https://img.shields.io/badge/AutoCAD-.NET_API-brightgreen)

A general-purpose entity filtering tool for AutoCAD, supporting quick filtering of entities by type, layer, color, and block name.

**Note**: This project uses an older version of IFoxCAD (now discontinued). Please use the recommended version in the project if not specifically needed. Some older AutoCAD versions cannot be properly debugged in Visual Studio 2022.

## Features

- **Entity Filter (EF)**: Filter selection set by entity type (e.g., lines, polylines, dimensions, and some custom entities)
- **Layer Filter (XF)**: Filter selection set by layer (supports multiple layer references)
- **Color Filter (CF)**: Filter selection set by color index (supports layer-following color)
- **Block Filter (BF)**: Filter selection set by block name (supports multiple block references)

### Build Requirements

- .NET Framework 3.5 / 4.0 / 4.5
- AutoCAD .NET API
- IFoxCAD Base Library

### Build Configuration

The project supports multi-target framework compilation:
- **net35**: For AutoCAD 2010-2012
- **net40**: For AutoCAD 2013-2014
- **net45**: For AutoCAD 2015-2024

The corresponding AutoCAD .NET API version will be automatically referenced based on the target framework.

### Usage

1. Compile the project to generate `GeneralFilterForACAD.dll`
2. Place the DLL file in AutoCAD's support file search path
3. Load the plugin in AutoCAD:
   - Method 1: Add the DLL to the startup group
   - Method 2: Use the `NETLOAD` command to load `GeneralFilterForACAD.dll`

**Note**: After loading the plugin, the plugin's directory will be added to AutoCAD's support file search path, so please do not place the plugin in sensitive paths (containing personal information, work files).

### Command List

| Command | Description |
|---------|-------------|
| `EF` | Entity Filter - Select entities to filter, reference entities determine filter type |
| `XF` | Layer Filter - Select entities to filter, reference entities determine target layer |
| `CF` | Color Filter - Select entities to filter, reference entities determine target color |
| `BF` | Block Filter - Select entities to filter, find block references of specified block name |

### Command Usage Example

The project has implemented custom algorithms to avoid using AutoCAD's UsePickupSet flag. Entities meeting filter conditions are retained until the next operation.

After completion, entities that meet the filter conditions will be automatically selected.

You can perform continuous filtering by selecting in advance, or continue filtering on the entities filtered by the previous filter.

For example, using the **EF (Entity Filter)** command:

```text
Command: EF
Select entities to filter: // Select entities to filter
Select type reference entities: // Select one or more reference entities to determine the entity type to keep
Command: XF - Continue picking with layer as condition for the next filter
```

### Convenience Features

Convenience features provided for developers:

- **Transaction Management**: Automatically listens to newly activated documents and does not trigger event subscription repeatedly
- **Automatic Command Listening**: Uses Linq to automatically add custom commands provided for AutoCAD in the Command class to the listening event

### Supported Filter Types

```csharp
public enum FilterType
{
    EntityFilter,   // Entity type filter
    ColorFilter,    // Color filter
    LayerFilter,    // Layer filter
    LinetypeFilter, // Linetype filter (not implemented)
    BlockFilter,    // Block filter
}
```

### Document Management

The project implements automatic document index management, supporting multi-document environment:

- Automatically assigns a unique index to each document
- Automatically cleans related data when document is activated

## Project Structure

```
GeneralFilterForACAD/
├── Command.cs           # Command class, defines user-callable filter commands
├── Init.cs             # Auto-load initialization class
├── GlobalUsings.cs     # Global using references
├── GeneralFilterForACAD.csproj  # Project file
└── README.md           # Project documentation (Chinese)
```

### Core Classes

#### `Core` Class

Core filtering logic class, provides static methods for entity filtering:

| Method | Description |
|--------|-------------|
| `GetOrAddIndex(Document)` | Get or create document index |
| `GetPath(int)` | Get document path by index |
| `AddEntity(int, ObjectId)` | Add entity to document's entity list |
| `RemoveDocument(Document)` | Remove document and related data |
| `Filter(...)` | Unified filtering interface |
| `FilterByEntity(...)` | Filter by entity type |
| `FilterByLayer(...)` | Filter by layer |
| `FilterByColor(...)` | Filter by color |
| `FilterByBlock(...)` | Filter by block name |

#### `Command` Class

Command class, provides AutoCAD command interface:

| Command Method | Description |
|----------------|-------------|
| `EntityFilter()` | Entity filter command |
| `LayerFilter()` | Layer filter command |
| `ColorFilter()` | Color filter command |
| `BlockFilter()` | Block filter command |

## Tech Stack

- **Language**: C# (.NET Framework)
- **Framework**: AutoCAD .NET API
- **Dependencies**: 
  - IFox.Basal.Source (0.5.2.4)
  - IFox.CAD.Source (0.5.2.4)

## License

This project is licensed under the [GNU Affero General Public License v3.0](LICENSE.txt).

## Author

[YanWuuuuu](https://github.com/YanWuuuuu)

## Related Links

- [AutoCAD .NET API Documentation](https://help.autodesk.com/view/OARX/2024/ENU/?guid=NET)
- [IFoxCAD Project](https://github.com/ifoxcompany/IFoxCAD)