# MSBuild Inline Task demo
This demo shows how to display a WPF window using `MSBuild` files only.
The real usage is to modify stuff with C# code during build process.

## How?
- Define a `UsingTask` to a `.target` or `.vcxproj` or `.csproj` file
```xml
<!--TaskName be your arbitary name-->
<UsingTask TaskName="HelloWorld" TaskFactory="RoslynCodeTaskFactory" AssemblyFile="$(MSBuildToolsPath)\Microsoft.Build.Tasks.Core.dll">
    <ParameterGroup>
        <!--Add parameters here-->
    </ParameterGroup>
    <Task>
        <!--Add assembly reference here-->
        <Reference Include="C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.7.2\PresentationFramework.dll"/>
        
        <!--Using Namespaces here-->
        <Using Namespace="System.Windows"/>

        <Code Type="Fragment" Language="cs"><![CDATA[
            //Your C# code here.
            //Actually all code here are inside a function, which you can see from the msbuild output temporary file
            //So you can't define struct or classes.
            //You can only define static functions here.
        ]]></Code>
    </Task>
</UsingTask>

<Target Name="Demo"> <!--Reference this target name in the "PropertyGroup" below-->
    <HelloWorld/>  <!--Your arbitary name-->
</Target>
```
- Add a `<PropertyGroup>` in that same file, and the thing inside it **varies** depending on your project type, if it's a C++ project (`.vcxproj` file), the following code execute the code **before** builds.
```xml
<PropertyGroup>
    <BeforeMidlCompileTargets>
        $(BeforeMidlCompileTargets);
        Demo  <!--Your "Target" name above-->
    </BeforeMidlCompileTargets>
</PropertyGroup>
```


## Result
Open a `Developer Powershell`, run `msbuild.exe`, and you shall see this.
![image](https://github.com/HO-COOH/MsBuild-Inline-Task-Demo/assets/42881734/bf728bc8-b6a7-45b0-b452-94601f7be8e6)

