# ![3mf logo](images/3mf_logo_50px.png) 3MF Production Extension

## Specification & Reference Guide











| **Version** | 1.1 |
| --- | --- |
| **Status** | Published |

## Disclaimer

THESE MATERIALS ARE PROVIDED "AS IS." The contributors expressly disclaim any warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to the materials. The entire risk as to implementing or otherwise using the materials is assumed by the implementer and user. IN NO EVENT WILL ANY MEMBER BE LIABLE TO ANY OTHER PARTY FOR LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THIS DELIVERABLE OR ITS GOVERNING AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND WHETHER OR NOT THE OTHER MEMBER HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

## Table of Contents

TBC

# Preface

## 1.1 About this Specification

This 3MF Production Extension specification is an extension to the core 3MF specification. This document cannot stand alone and only applies as an addendum to the core 3MF specification. Usage of this and any other 3MF extensions follow an a la carte model, defined in the core 3MF specification.

Part I, "3MF Documents," presents the details of the primarily XML-based 3MF Document format. This section describes the XML markup that defines the composition of 3D documents and the appearance of each model within the document.

Part II, "Appendixes," contains additional technical details and schemas too extensive to include in the main body of the text as well as convenient reference information.

The information contained in this specification is subject to change. Every effort has been made to ensure its accuracy at the time of publication.

This extension MUST be used only with Core specification 1.0.

## Document Conventions

See [the 3MF Core Specification conventions](https://github.com/3MFConsortium/spec_core/blob/master/3MF%20Core%20Specification.md#document-conventions).

## Language Notes

See [the 3MF Core Specification language notes](https://github.com/3MFConsortium/spec_core/blob/master/3MF%20Core%20Specification.md#language-notes).

## Software Conformance

See [the 3MF Core Specification software conformance](https://github.com/3MFConsortium/spec_core/blob/master/3MF%20Core%20Specification.md#software-conformance).

# Part I: 3MF Production Extension

# Chapter 1. Overview of Additions

This document describes new non-object resources, as well as attributes to the build section for uniquely identifying parts within a particular 3MF package. If not explicitly stated otherwise, each of these resources is OPTIONAL for producers, but MUST be supported by consumers that specify support for the Production Extension of 3MF.

In order to allow for the use of 3MF in high production printing environments, several additions are needed to efficiently support packed build platforms and ensure integrity of the payload:

- Enable the 3MF <build> elements to address objects in separate files within the 3MF package
- Identify each build, each object and each copy of a part with unique identifiers

A consumer supporting the production extension MUST be able to consume non-extended core 3MFs, even if this is not as efficient. As the production extension is just a reorganization of data, a consumer MAY convert a generic core 3MF into a "production extended 3MF" before internally processing the data.

In order to avoid data loss while parsing, a 3MF package which uses referenced objects MUST enlist the production extension as "required extension", as defined in the core specification.

# Chapter 2. Model Relationships

The primary emphasis of this extension is the possibility to store model data in files separate from the root model file and to allow the root model file's build element to reference those resources. This structural approach enables three primary advantages for producers and consumers of 3MF packages with large numbers of individual models:

- The build directive in the root model file can be parsed by consumers without having to parse any actual model data.
- Moving from a single-part 3MF (e.g. from a design application) into a high-part-density 3MF build can be largely a pass-through from the original 3MF to the "production" 3MF.
- Parsing the model objects from individual XML files will often require fewer resources than parsing a monolithic model file that could be more that 500MB in size.

The root model part MAY have relationships to other model parts, whose object resources can be referenced by the parent model stream using the "path" attribute on a build item element or within a component.

As defined in the core 3MF spec, only the build section of the root model file contains valid build information. Other model streams SHOULD contain empty build sections. Every consumer MUST ignore the build section entries of all referenced child model files.

Further, only a component element in the root model file may contain a path attribute. Non-root model file components MUST only reference objects in the same model file.

These two limitations ensure there is only a single level of "depth" to multi-file model relationships within a package and explicitly prevents complex or circular object references.

# Chapter 3. Production Extension Data Details

![](3MF_Production_Extension_Spec_v1.1_html_a21621bce13d26f8.gif)
_Figure 3–1. A typical production 3MF Document with multiple model streams_

## 3.1 The Path Attribute

### 3.1.1 Item

Within the <item> element of the build section in the root model, there is a new, optional attribute called "path". Path is an absolute path to the target model file inside the 3MF container that contains the target object. When the path attribute is used, objectid becomes a reference to the object within the referenced model.

![item](3MF_Production_Extension_Spec_v1.1_html_f514a82f6583dc37.png) |

##### Attributes

| Name | Type | Use | Default | Fixed | Annotation |
| --- | --- | --- | --- | --- | --- |
| path | **ST\_Path** | required | | | A file path to the model file being referenced. The path is an absolute path from the root of the 3MF container. |
| objectid | **ST\_ResourceID** | required | | | Objectid is part of the core 3MF specification, and its use in the production extension the same: objectid indexes into the model file to the object with the corresponding id. The only difference is that the path attribute identifies the target file from which to load the specified object. |

### 3.1.2 Component

Within the <component> elements of component-based objects, the "path" attribute references objects in non-root model files. Path is an absolute path to the target model file inside the 3MF container that contains the target object. The use of the path attribute in a component element is ONLY valid in the root model file.

![component](3MF_Production_Extension_Spec_v1.1_html_9a645498f8c1bc2c.png) |

##### Attributes

| Name | Type | Use | Default | Fixed | Annotation |
| --- | --- | --- | --- | --- | --- |
| path | **ST\_Path** | required | | | A file path to the model file being referenced. The path is an absolute path from the root of the 3MF container. |
| objectid | **ST\_ResourceID** | required | | | Objectid is part of the core 3MF specification, and its use in the production extension the same: objectid indexes into the model file to the object with the corresponding id. The only difference is that the path attribute identifies the target file from which to load the specified object. |

## 3.2 Path Usage

The path attribute is optional, even for 3MF containers that claim support for the production extension. It is possible to construct a 3MF package with objects both in the root model file and in other model files in the container.

Some considerations for using multiple file 3MF constructions with the path attribute:

Objects referenced by the path and objected attribute of the build item inherit all of their properties from their own object definition and resources. All of the resources associated with the referenced object (textures, materials, thumbnails, name, part number, etc.) must come from the referenced object file.

The model level Metadata element is only valid in the root model file of a 3MF package. All Metadata elements in other model files in a 3MF package will be ignored.

A path attribute can reference an object in a target file that is made-up of components. In this case, the same processing rules apply as with a local component object: the object transforms are relative to the item transform and consumers MUST not alter the relative transformations within the component objects.

A root model based component can be partially, or fully, composed of objects from different model files. This allows a single component reference to place objects from multiple files (including the root model file) into a virtual assembly that it bound to the origin of the build item transformation. Any consumer of a 3MF package that contains path attributes in components in a non-root model file MUST generate an error for that package.

Because there can be only a single build section (in the root model), there is at most a single level of referenced objects in any 3MF container. This eliminates the possibility for complex or circular object references between model files in 3MF containers.

## 3.3 OPC Relation Files

All model files in the 3MF package must be referenced in .rels files in order to conform with OPC standards. The root model file MUST always be referenced in the root .rels file with Id="0". Example root .rels file:
```xml
<?xml version="1.0" encoding="utf-8"?>
<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">
<Relationship Type="http://schemas.microsoft.com/3dmanufacturing/2013/01/3dmodel" Target="/3D/build.model" Id="rel0" />
<Relationship Type="http://schemas.openxmlformats.org/package/2006/relationships/metadata/thumbnail" Target="/Metadata/thumbnail.png" Id="rel4" />
</Relationships>
```

Non-root model files must not be referenced from the root .rels file. Referenced model files must be included the .rels file from the referencing model file according to the part relationship defined in OPC. For example, assuming that the root model file in the /3D folder is named model.model, the non-root model file references must be in the /3D/\_rels/model.model.rels file:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">
<Relationship Type="http://schemas.microsoft.com/3dmanufacturing/2013/01/3dmodel" Target="/3D/object1.model" Id="rel1" />
<Relationship Type="http://schemas.microsoft.com/3dmanufacturing/2013/01/3dmodel" Target="/3D/object2.model" Id="rel2" />
<Relationship Type="http://schemas.microsoft.com/3dmanufacturing/2013/01/3dmodel" Target="/3D/object3.model" Id="rel3" />
</Relationships>

```

# Chapter 4. Identifying Build Components

Components of 3MF containers need to be uniquely identifiable in order to ensure tracking of builds and parts through build processes. Within a given 3MF container, build items can be uniquely identified by providing a UUID for each <item>. Individual objects (models) in a build can be uniquely identified with a UUID on each <object> element. Individual component-based parts can be identified using a UUID attribute on <component> elements.

The Production Extension REQUIRES that both <item> and <component> elements include the UUID attribute as a mechanism to identify model instances being used in the 3MF package.

In some environments, it is crucial that the builds themselves can be uniquely identified within, and even across, different physical printers. In order to support cross-printer and cross-print-job build identification, <build> elements in the root model part REQUIRE a UUID attribute.

## 4.1 Build

Element **<build>**

![build](3MF_Production_Extension_Spec_v1.1_html_25694ee685ab8ceb.png) |

| Name | Type | Use | Default | Fixed | Annotation |
| --- | --- | --- | --- | --- | --- |
| uuid | **ST\_UUID** | required | | | A universally unique ID that allows the build to be identified over time and across physical clients and printers. |


Producers MUST provide a UUID in the root model file build element to ensure that a 3MF package can be tracked across uses by various consumers.

### 4.1.1 Item

Element **<item>**

![item](3MF_Production_Extension_Spec_v1.1_html_c2b54cf4abf9b8ac.png) |

| Name | Type | Use | Default | Fixed | Annotation |
| --- | --- | --- | --- | --- | --- |
| UUID | **ST\_UUID** | required | | | A globally unique identifier for each item in the 3MF package which allows producers and consumers to track part instances across 3MF packages. |

Producers MUST include UUID's for all build items for traceability across 3MF packages.

## 4.2 Object

Element < **object>**

![object](3MF_Production_Extension_Spec_v1.1_html_d254e86223440f10.png) |

| Name | Type | Use | Default | Fixed | Annotation |
| --- | --- | --- | --- | --- | --- |
| UUID | **ST\_UUID** | required | | | A globally unique identifier for each <object> in the 3MF package which allows producers and consumers to track object instances across 3MF packages. In the case that an <object> is made up of <components>, the UUID represents a unique ID for that collection of object references. |

Producers MUST include UUID's in all <object> references to ensure that each object can be reliably tracked.

>**Note:** As for the use case of production data, the uniqueness properties are sufficient, this specification tries to avoid the technical details about conforming UUIDs according to [ITU-T X.667 ISO/IEC 9834-8:2004](http://www.itu.int/ITU-T/studygroups/com17/oid.html). "Unique identifier" always may mean any of the four UUID variants described in IETF RFC 4122, which includes Microsoft GUIDs as well as time-based UUIDs.

### 4.2.1 Component

Element < **component>**

![component](3MF_Production_Extension_Spec_v1.1_html_ba9641662f6aee2a.png) |

| Name | Type | Use | Default | Fixed | Annotation |
| --- | --- | --- | --- | --- | --- |
| UUID | **ST\_UUID** | required | | | A globally unique identifier for each object component in the 3MF package which allows producers and consumers to track part instances across 3MF packages. |

Producers MUST include UUID's in all component-based object references to ensure that each instance of an object can be reliably tracked.

# Part II. Appendixes

# Appendix A. Glossary

See [the 3MF Core Specification glossary](https://github.com/3MFConsortium/spec_core/blob/master/3MF%20Core%20Specification.md#appendix-a-glossary).

# Appendix B. 3MF Production Extension Schema

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns="http://schemas.microsoft.com/3dmanufacturing/production/2015/06" xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:xml="http://www.w3.org/XML/1998/namespace" targetNamespace="http://schemas.microsoft.com/3dmanufacturing/production/2015/06" elementFormDefault="unqualified" attributeFormDefault="unqualified" blockDefault="#all">
    <xs:import namespace="http://www.w3.org/XML/1998/namespace" schemaLocation="http://www.w3.org/2001/xml.xsd"/>

    <!-- Complex Types -->

    <xs:complexType name="CT_Item">
        <xs:attribute name="objectid" type="ST_ResourceID" use="required"/>
        <xs:attribute name="path" type="ST_Path"/>
        <xs:attribute name="UUID" type="ST_UUID" use="required"/>
        <xs:anyAttribute namespace="##other" processContents="lax"/>
    </xs:complexType>

     <xs:complexType name="CT_Component">
        <xs:attribute name="objectid" type="ST_ResourceID" use="required"/>
        <xs:attribute name="path" type="ST_Path"/>
        <xs:attribute name="UUID" type="ST_UUID" use="required"/>
        <xs:anyAttribute namespace="##other" processContents="lax"/>
     </xs:complexType>

    <xs:complexType name="CT_Object">
        <xs:choice>
            <xs:element ref="mesh"/>
            <xs:element ref="components"/>
        </xs:choice>
        <xs:attribute name="id" type="ST_ResourceID" use="required"/>
        <xs:attribute name="UUID" type="ST_UUID" use="required"/>
        <xs:attribute name="type" type="ST_ObjectType" default="model"/>
        <xs:attribute name="pid" type="ST_ResourceID"/>
        <xs:attribute name="pindex" type="ST_ResourceIndex"/>
        <xs:attribute name="thumbnail" type="ST_UriReference"/>
        <xs:attribute name="partnumber" type="xs:string"/>
        <xs:attribute name="name" type="xs:string"/>
        <xs:attribute name="slicestackid" type="ST_ResourceID"/>
        <xs:attribute name="slicepath" type="xs:string"/>
        <xs:attribute name="meshresolution" type="xs:string">
            <xs:simpleType>
                <xs:restriction base="xs:string">
                    <xs:enumeration value="build" />
                    <xs:enumeration value="preview" />
                    <xs:enumeration value="boundingbox" />
                </xs:restriction>
            </xs:simpleType>
        </xs:attribute>
        <xs:anyAttribute namespace="##other" processContents="lax"/>
    </xs:complexType>

    <!-- Simple Types -->

    <xs:simpleType name="ST_Path">
        <xs:restriction base="xs:string">
        </xs:restriction>
    </xs:simpleType>

    <xs:simpleType name="ST_UUID">
        <xs:restriction base="xs:string">
            <xs:pattern value="[a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12}"/>
        </xs:restriction>
    </xs:simpleType>

</xs:schema>
```

# Appendix C. Standard Namespaces and Content Types

## C.1 Namespaces

Production http://schemas.microsoft.com/3dmanufacturing/production/2015/06

# References

See [the 3MF Core Specification references](https://github.com/3MFConsortium/spec_core/blob/master/3MF%20Core%20Specification.md#references).

Copyright 3MF Consortium 2018.
