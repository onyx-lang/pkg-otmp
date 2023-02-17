# Onyx TeMPlates

While the package name is less than sufficient, this library
provides a string templating system, similar to Mustache, Handlebars,
Jinja, etc. It is written entirely in Onyx and uses the power of
Onyx's `any` to make variables and arbitrary values easy to use.

## Example Usage

```onyx

//
// First, create a registry where all of the templates will be
// stored. This acts as the one way to talk to the templating
// library.
//
registry := otmp.registry();

//
// Next, load all files in a directory recursively. This will
// automatically register the template names as their relative
// path, with the final extension. For example,
//
//     ./templates/index.html            -> index
//     ./templates/partials/profile.html -> partials/profile
//
registry->load_directory("./templates", ".html");

//
// Now we can render a template!
//
// To keep the code as abstract as possible, this procedure simply
// takes a io.Writer, where it will write the output of the template
// generation. In this example, the writer is simply the standard output,
// but in other use cases it might be a request body, or a file.
//
// The final argument is where you provide the values for the variables
// used in your template. This is given as a *pointer* to a structure
// (normally an anonymous structure). The members names of the structure
// become the variable names in the template.
//
registry->render_template("index", ^stdio.print_writer, ^.{
    var1 = "Hello!",
    array = .[ 1, 2, 3, 4 ]
});

```

## Example Template

`base.html`
```html
<html>
    <head>
        <title>{% block "title" %}</title>
    </head>

    <body>
        {% block "content" %}
    </body>
</html>
```

`index.html`
```html
<!-- Unlike Jinja/Mustache/etc, OTMP has you declare the contents of blocks
     BEFORE you "extend" another template. -->

{{ block "title" }} This is the title! {{ endblock }}

{{ block "content" }}
    
    <!-- Use "$" to access variables -->
    <h1>{% $var1 %}</h1>

    <ul>
        <!-- foreach iterates over the contents of an array. -->
        {{ foreach $n in $array }}
            <li>{% $n %}</li>
        {{ endforeach }}
    </ul>

{{ endblock }}

<!-- Now that the blocks are defined, extend the base template which
     will use the contents of the blocks defined above -->
{{ extends "base" }}

```

