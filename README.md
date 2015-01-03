_**IMPORTANT NOTE:**_ This project is currently in beta and the documentation is currently incomplete. Please bear with us while the documentation is being written.

####SuperScript offers a means of declaring assets in one part of a .NET web solution and have them emitted somewhere else.


When developing web solutions, assets such as JavaScript declarations or HTML templates are frequently written in a location that differs from their desired output location.

For example, all JavaScript declarations should ideally be emitted together just before the HTML document is closed. And if caching is preferred then these declarations should be in an external file with caching headers set.

This is the functionality offered by SuperScript.



##Pre-compile JavaScript-Templates HTML Templates

While [`SuperScript.Templates`](https://github.com/Supertext/SuperScript.Templates) allows developers to declare instances 
of [`TemplateDeclaration`](https://github.com/Supertext/SuperScript.Templates/blob/master/Declarables/TemplateDeclaration.cs),
and [`SuperScript.Templates.Mvc`](https://github.com/Supertext/SuperScript.Templates.Mvc) and 
[`SuperScript.Templates.WebForms`](https://github.com/Supertext/SuperScript.Templates.WebForms) allow HTML templates written 
on ASP.NET Razor views or _.aspx_ files and have them automatically converted into instances of 
[`TemplateDeclaration`](https://github.com/Supertext/SuperScript.Templates/blob/master/Declarables/TemplateDeclaration.cs),
this library adds pre-compiling of these templates into [JavaScript Templates](https://github.com/blueimp/JavaScript-Templates) 
HTML templates.


##What's in this project?

`SuperScript.Templates.JavaScriptTemplates.Modifiers.Converters.JavaScriptTemplatesStringify`

This is an implementation of [`SuperScript.Templates.Modifiers.Converters.TemplateStringify`](https://github.com/Supertext/SuperScript.Templates/blob/master/Modifiers/Converters/TemplateStringify.cs) 
which itself is an implementation of [`SuperScript.Modifiers.Converters.CollectionConverter`](https://github.com/Supertext/SuperScript.Common/blob/master/Modifiers/Converters/CollectionConverter.cs). 
A `CollectionConverter` is a class which is enlisted by an emitter (_i.e._, an instance of [`IEmitter`](https://github.com/Supertext/SuperScript.Common/blob/master/Emitters/IEmitter.cs)) 
is passed a collection of declarations and outputs a modified version of this collection.

In this case, `JavaScriptTemplatesStringify` converts each of the passed declarations by returning a pre-compiled version of each.

To use this `CollectionConverter`, describe an instance of `IEmitter` and reference this class.

In a _web.config_

```XML
<emitter key="templates">
  <converters>
    <!-- reference the JavaScriptTemplatesStringify CollectionConverter -->
    <modifier type="SuperScript.Templates.JavaScriptTemplates.Modifiers.Converters.JavaScriptTemplatesStringify, SuperScript.Templates.JavaScriptTemplates">
      <properties>
        <!-- Setting 'PreCompile' (which is on the TemplateStringify superclass) here allows the 
             pre-compiling to be turned on or off without re-building the project  -->
        <property name="PreCompile" value="true" type="System.Boolean, mscorlib" />
      </properties>
    </modifier>
  </converters>
  <postModifiers>
    ...
  </postModifiers>
  <writers>
    ...
  </writers>
</emitter>
```

In code

```C#
// instantiate a standard emitter
var emitTemplates = new StandardEmitter();

// assign a key for which to reference this emitter
emitTemplates.Key = "templates";

// now add the CollectionConverter to pre-compile the JavaScript-Templates templates
var templStringify = new SuperScript.Templates.JavaScriptTemplates.Modifiers.Converters.JavaScriptTemplatesStringify();
templStringify.PreCompile = true;

// add this CollectionConverter to our emitter
emitTemplates.Converter = templStringify;

// add an instance of HtmlWriter which this emitter will use to write the output of all TemplateDeclaration instances
emitTemplates.HtmlWriter = new SuperScript.Templates.Modifiers.Writers.TemplateStringifyHtml();

// add our emitter to the collection of application-wide emitters
SuperScript.Configuration.Settings.Instance.Emitters.Add(emitTemplates);
```


##Dependencies
There are a variety of SuperScript projects, some being dependent upon others.

* [`SuperScript.Common`](https://github.com/Supertext/SuperScript.Common)

  This library contains the core classes which facilitate all other SuperScript modules but which won't produce any meaningful output on its own.

* [`SuperScript.Templates`](https://github.com/Supertext/SuperScript.Templates)

  This library contains functionality for making HTML template-specific declarations.

##Alternatives

[`SuperScript.Templates.jQueryTmpl`](https://github.com/Supertext/SuperScript.Templates.jQueryTmpl) pre-compiles
HTML templates into [jQuery.tmpl](https://github.com/BorisMoore/jquery-tmpl) HTML templates.

<br>
<hr>

`SuperScript.Templates.JavaScriptTemplates` has been made available under the [MIT License](https://github.com/Supertext/SuperScript.Templates.JavaScriptTemplates/blob/master/LICENSE).
