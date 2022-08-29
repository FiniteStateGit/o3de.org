---
linktitle: System Overview
title: "Material System Overview"
description: "Materials contain data that control how model surfaces appear in a 3D environment."
toc: false
weight: 100
---

**Materials** contain data that control how model surfaces appear in a 3D environment. All materials have a **material type**, which classifies materials that share the same properties and shader code such as hard surfaces, cloth, or skin. Materials inherit properties either directly from the material type or from other *parent* materials. Each `.material` file only needs to store property values that differ from its parent.

<!-- SVG file edited using https://app.diagrams.net/ -->
![Material Files Diagram](/images/atom-guide/materials/material-file-diagram.svg)

Materials and material types are stored as data items in JSON files. The Atom Material Builder converts the data files into material assets. The material assets are then consumed by the application and applied to a mesh's surface. 

## Materials
A **material** is a data item that references a material type and defines the material's property values. The material must reference a material type, which defines how the material works and what properties are available. Alternatively, a material might reference another *parent* material and inherit its property values. The material inheritance tree can go as deep as needed. 

Material files (`*.material`) are in JSON format. They can be edited using the **Material Editor** or authored directly. The file is in a simple JSON format and can be authored or machine-generated by scripting outside the Material Editor.

For more information, see [Material File Specification](/docs/atom-guide/look-dev/materials/material-file-spec/).  

### Material File Example

In the following example, the material uses the **StandardPBR** material type and contains the properties `baseColor`, `roughness`, and `normal`. 

```json
{
    "materialType": "Materials/Types/StandardPBR.materialtype",
    "materialTypeVersion": 6,
    "propertyValues": {
        "baseColor.textureMap": "Textures/Default/default_basecolor.tif",
        "roughness.textureMap": "Textures/Default/default_roughness.tif",
        "normal.factor": 0.8,
        "normal.textureMap": "Textures/Default/default_normal.tif"
    }
}
```

{{< image-width "/images/atom-guide/materials/simple-example.jpg" "720" "Example Material Screenshot" >}}

{{< note >}}
The textures in the sample above can be found in `Gems/Atom/Feature/Common/Assets/Textures/Default`.
{{< /note >}}

## Material Types
A **material type** is a data item that includes everything that's needed to describe how to render a mesh:  
- A set of *material property* definitions
- Links to shaders
- Scripts that describe how to consume the material properties
- Metadata for the Material Editor to use when displaying material properties

Material type files (`*.materialtype`) are in JSON format and are authored directly. This file links together several other files to form a complete material type definition, such as `*.shader`, `*.azsl`, and `*.lua` files.

For more information, see [Material Type File Specification](./material-type-file-spec).  

{{< note >}}
A node graph based tool for authoring material types is in development. Until that's available, material type are authored directly.
{{< /note >}}

## Atom PBR Material Types
*Physically based rendering (PBR)* is a rendering technique that simulates realistic interaction between materials and light. Atom provides several core material types based on industry standard workflows using metallic and roughness properties. Atom's material system is also capable of rendering non-photo-realistic techniques and special effects through alternate material types. Users can author their own material types to suit their needs.

For more information on the available PBR material types, see [Physically-based Rendering (PBR)](/docs/atom-guide/look-dev/materials/pbr/)<!-- and [Working with StandardPBR materials](./material-build-pipeline)DRAFT TOPIC-->. 

## Material Asset Processing

The Asset Processor loads material and material type source files and saves corresponding product files in the cache. The runtime loads these files and uses them to render models. See [Asset Processing](/docs/user-guide/assets/pipeline/asset-processing/) for background information on this topic.

A material type source file (`*.materialtype`) produces a Material Type Asset (`*.azmaterialtype`) in the cache. It contains property layout information, a list of shaders to use, and possibly functors for special processing.

A material source file (`*.material`) produces a Material Asset (`*.azmaterial`) in the cache. Every Material Asset references a single Material Type Asset. It contains the inherited tree of material property values, compressed it into a single list. Material Assets can be referenced by Model Assets and/or assigned to a model using a Material component in the **Open 3D Engine (O3DE) Editor**. 

Other source files can produce Material Assets as well. For example, *.fbx* files which are commonly used to store models created using 3D modeling software may also contain material data. O3DE's **Scene Builder** will process these files to produce both Model Assets (`.azmodel`) and Material Assets (`.azmaterial`). Note that these Material Assets do not have a corresponding .material file in the source folder, but can still be used by the Mesh Component and/or Material Components for rendering.

Material Assets can create any number of material instances at runtime for the renderer to use. Using a Material component, you can change the property values of a material instance without impacting other instances of the same material. Unlike a Material Asset, a material instance only exists in memory and not on disk. 

<!-- SVG file edited using https://app.diagrams.net/ -->
![Material Assets Diagram](/images/atom-guide/materials/material-asset-diagram.svg)