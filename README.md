[![Chat on Gitter](https://img.shields.io/gitter/room/fody/fody.svg?style=flat)](https://gitter.im/Fody/Fody)
[![NuGet Status](http://img.shields.io/nuget/v/JetBrainsAnnotations.Fody.svg?style=flat)](https://www.nuget.org/packages/JetBrainsAnnotations.Fody/)
![Badge](https://tom-englert.visualstudio.com/_apis/public/build/definitions/75bf84d2-d359-404a-a712-07c9f693f635/14/badge)

## This is an add-in for [Fody](https://github.com/Fody/Fody/) 

![Icon](https://raw.github.com/Fody/JetBrainsAnnotations/master/Icons/package_icon.png)

Converts all JetBrains ReSharper code annotation attributes to [External Annotations](https://www.jetbrains.com/help/resharper/Code_Analysis__External_Annotations.html), 
so you can provide R# annotations to 3rd parties but don't need to deploy JetBrainsAnnotations.dll. 


## The nuget package

https://nuget.org/packages/JetBrainsAnnotations.Fody/

    PM> Install-Package JetBrainsAnnotations.Fody


## What are JetBrains Annotations

The assembly JetBrainsAnnotations.dll is shipped as a [nuget package](https://www.nuget.org/packages/JetBrains.Annotations/).
It provides standard JetBrains ReSharper code annotation attribute implementations. 
This allows you to better leverage the ReSharper intellisense.

To provide the annotations to 3rd parties you must define [`JETBRAINS_ANNOTATIONS`](https://www.jetbrains.com/help/resharper/Code_Analysis__Annotations_in_Source_Code.html) to include the attributes in your assembly.
However now you have a reference and would need to ship the JetBrainsAnnotations.dll with your product. 

This Fody plugin converts all attributes to an external annotations XML file on the fly, so you 
can ship just the text file with your assembly and don't need to reference JetBrainsAnnotations.

For more information 

 * http://www.jetbrains.com/resharper/webhelp/Code_Analysis__External_Annotations.html 
 * http://www.jetbrains.com/resharper/features/code_analysis.html#Annotated_Framework


## What it actually does to your assembly

 * For each attribute defined in JetBrainsAnnotations.dll it adds an entry to your
   assemblies external annotations XML file and removes the usage of the attribute from your assembly.
 * Removes the reference to JetBrainsAnnotations.dll

## What it actually does to your project

* Updates `<project name>.ExternalAnnotations.xml` every time you compile.

  NOTE: To make your annotations available to 3rd parties, you must ship this file along with the assembly.

* If your project is set to generate an XML documentation file, the documentation is extended with 
  the annotation attributes. 
  

## What you may need to change manually

* Add the `<project name>.ExternalAnnotations.xml` to your project manually and mark it as content + copy to output.
* Mark the reference to `Jetbrains.Annotations.dll` as `Copy Local => False`, so it won't get copied to your 
  target directory and eventually get picked up by installers.
* If you deploy your project as a NuGet package, add `developmentDependency="true"` to the 
  JetBrains.Annotations package entry in your projects `packages.config` files, else NuGet will list JetBrains.Annotations 
  as a dependency of your package:
    ```xml
    <package id="JetBrains.Annotations" version="11.0.0" targetFramework="net452" developmentDependency="true" />
    ```
  or if you are using a `PackageReference` in your project files, mark it as private assests:
    ```xml
    <PackageReference Include="JetBrains.Annotations" Version="*">
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    ```

## Using embedded ReSharper annotations

If your project [embeds ReSharper annotations](https://www.jetbrains.com/help/resharper/Code_Analysis__Annotations_in_Source_Code.html#embedding-declarations-of-code-annotations-in-your-source-code),
or references another project that has such embedded annotations, you can modify the Fody configuration file to have JetBrainsAnnotations
recognize this. In your `FodyWeavers.xml`, use:

```xml
<Weavers>
  <JetBrainsAnnotations assembly="MyEmbeddedAnnotations" remove="true"/> 
</Weavers>
```

where the `assembly` attributes indicates the simple name of the assembly containing embedded annotations, and `remove`
optionally indicates (default is `remove='false'`) whether to remove the reference to `assembly`, as JetBrainsAnnotations does for `Jetbrains.Annotations.dll`.

The `assembly` may also refer to the same assembly being weaved, if ReSharper annotations are embedded in the same assembly for
which you're generating external annotations. In which case, the `remove` attribute has no effect.

The embedded ReSharper annotations can be either `internal` or `public`.

## Icon

<a href="http://thenounproject.com/noun/fighter-jet/#icon-No9259" target="_blank">Fighter Jet</a> designed by <a href="http://thenounproject.com/lukefirth" target="_blank">Luke Anthony Firth</a> from The Noun Project.
