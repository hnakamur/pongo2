# [pongo](https://en.wikipedia.org/wiki/Pongo_%28genus%29)2

[![GoDoc](https://godoc.org/github.com/flosch/pongo2?status.png)](https://godoc.org/github.com/flosch/pongo2)
[![Build Status](https://travis-ci.org/flosch/pongo2.svg?branch=master)](https://travis-ci.org/flosch/pongo2)
[![Coverage Status](https://coveralls.io/repos/flosch/pongo2/badge.png?branch=master)](https://coveralls.io/r/flosch/pongo2?branch=master)
[![GitTip](http://img.shields.io/badge/gittip-support%20pongo-brightgreen.svg)](https://www.gittip.com/flosch/)

pongo2 is the successor of [pongo](https://github.com/flosch/pongo), a Django-syntax like templating-language.

Install/update using `go get` (no dependencies required by pongo2):
```
go get -u github.com/flosch/pongo2
```

Please use the [issue tracker](https://github.com/flosch/pongo2/issues) if you're encountering any problems with pongo2 or if you need help with implementing tags or filters ([create a ticket!](https://github.com/flosch/pongo2/issues/new)). If possible, please use [playground](https://www.florian-schlachter.de/pongo2/) to create a short test case on what's wrong and include the link to the snippet in your issue.

**New**: [Try pongo2 out in the pongo2 playground.](https://www.florian-schlachter.de/pongo2/)

## First impression of a template

```HTML+Django
<html><head><title>Our admins and users</title></head>
{# This is a short example to give you a quick overview of pongo2's syntax. #}

{% macro user_details(user, is_admin=false) %}
	<div class="user_item">
		<!-- Let's indicate a user's good karma -->
		<h2 {% if (user.karma >= 40) || (user.karma > calc_avg_karma(userlist)+5) %}
			class="karma-good"{% endif %}>
			
		     	<!-- This will call user.String() automatically if available: -->
			{{ user }}
		</h2>

		<!-- Will print a human-readable time duration like "3 weeks ago" -->
		<p>This user registered {{ user.register_date|naturaltime }}.</p>
		
		<!-- Let's allow the users to write down their biography using markdown;
		     we will only show the first 15 words as a preview -->
		<p>The user's biography:</p>
		<p>{{ user.biography|markdown|truncatewords_html:15|safe }}
			<a href="/user/{{ user.id }}/">read more</a></p>
		
		{% if is_admin %}<p>This user is an admin!</p>{% endif %}
	</div>
{% endmacro %}

<body>
	<!-- Make use of the macro defined above to avoid repetitive HTML code
	     since we want to use the same code for admins AND members -->
	
	<h1>Our admins</h1>
	{% for admin in adminlist %}
		{{ user_details(admin, true) }}
	{% endfor %}
	
	<h1>Our members</h1>
	{% for user in userlist %}
		{{ user_details(user) }}
	{% endfor %}
</body>
</html>
```

## Development status

**Latest stable release**: v1.0 (`go get -u gopkg.in/flosch/pongo2.v1` / [`v1`](https://github.com/flosch/pongo2/tree/v1)-branch) [[read the announcement](https://github.com/flosch/pongo2/releases/tag/v1.0)]

**Current development**: v1.1 (`master`-branch)

| Topic                                | Status                                                                                 |
| ------------------------------------ | -------------------------------------------------------------------------------------- |       
| Django version compatibility:        | [1.7](https://docs.djangoproject.com/en/1.7/ref/templates/builtins/)                  |
| *Missing* (planned) **filters**:     | none ([hints](https://github.com/flosch/pongo2/blob/master/filters_builtin.go#L3))     | 
| *Missing* (planned) **tags**:        | none ([hints](https://github.com/flosch/pongo2/blob/master/tags.go#L3))                |

Please also have a look on the [caveats](https://github.com/flosch/pongo2#caveats) and on the [official add-ons](https://github.com/flosch/pongo2#official).

## Features (and new in pongo2)

 * Entirely rewritten from the ground-up.
 * [Advanced C-like expressions](https://github.com/flosch/pongo2/blob/master/template_tests/expressions.tpl).
 * [Complex function calls within expressions](https://github.com/flosch/pongo2/blob/master/template_tests/function_calls_wrapper.tpl).
 * [Easy API to create new filters and tags](http://godoc.org/github.com/flosch/pongo2#RegisterFilter) ([including parsing arguments](http://godoc.org/github.com/flosch/pongo2#Parser))
 * Additional features:
    * Macros (see [template_tests/macro.tpl](https://github.com/flosch/pongo2/blob/master/template_tests/macro.tpl))

## Recent API changes within pongo2

If you're using pongo2, you might be interested in this section. Since pongo2 is still in beta, there could be (backwards-incompatible) API changes over time. To keep track of these and therefore make it painless for you to adapt your codebase, I'll list them here.

 * Two new helper functions: [`RenderTemplateFile()`](https://godoc.org/github.com/flosch/pongo2#RenderTemplateFile) and [`RenderTemplateString()`](https://godoc.org/github.com/flosch/pongo2#RenderTemplateString).
 * `Template.ExecuteRW()` is now [`Template.ExecuteWriter()`](https://godoc.org/github.com/flosch/pongo2#Template.ExecuteWriter)
 * `Template.Execute*()` functions do now take a `pongo2.Context` directly (no pointer anymore).

## How you can help

 * Write [filters](https://github.com/flosch/pongo2/blob/master/filters_builtin.go#L3) / [tags](https://github.com/flosch/pongo2/blob/master/tags.go#L4) (see [tutorial](https://www.florian-schlachter.de/post/pongo2/)) by forking pongo2 and sending pull requests
 * Write/improve code tests (use the following command to see what tests are missing: `go test -v -cover -covermode=count -coverprofile=cover.out && go tool cover -html=cover.out`)
 * Write/improve template tests (see the `template_tests/` directory)
 * Write middleware, libraries and websites using pongo2. :-)

# Documentation

For a documentation on how the templating language works you can [head over to the Django documentation](https://docs.djangoproject.com/en/dev/topics/templates/). pongo2 aims to be compatible with it.

You can access pongo2's API documentation on [godoc](https://godoc.org/github.com/flosch/pongo2).

## Blog post series

 * [pongo2 1.0 released](https://www.florian-schlachter.de/post/pongo2-10/) [August 8th 2014]
 * [pongo2 playground](https://www.florian-schlachter.de/post/pongo2-playground/) [August 1st 2014]
 * [Release of pongo2 1.0-rc1 + pongo2-addons](https://www.florian-schlachter.de/post/pongo2-10-rc1/) [July 30th 2014]
 * [Introduction to pongo2 + migration- and "how to write tags/filters"-tutorial.](https://www.florian-schlachter.de/post/pongo2/) [June 29th 2014]

## Caveats 

### Filters

In general, if any **filter** is outputting unsafe characters (e. g. HTML tags in filter `linebreaks`), you will have to apply the "safe" filter on it afterwards currently.
It is *not* done automatically. 

 * **date** / **time**: The `date` and `time` filter are taking the Golang specific time- and date-format (not Django's one) currently. [Take a look on the format here](http://golang.org/pkg/time/#Time.Format).
 * **stringformat**: `stringformat` does **not** take Python's string format syntax as a parameter, instead it takes Go's. Essentially `{{ 3.14|stringformat:"pi is %.2f" }}` is `fmt.Sprintf("pi is %.2f", 3.14)`.
 * **escape** / **force_escape**: Unlike Django's behaviour, the `escape`-filter is applied immediately. Therefore there is no need for a `force_escape`-filter yet.

### Tags

 * **for**: All the `forloop` fields (like `forloop.counter`) are written with a capital letter at the beginning. For example, the `counter` can be accessed by `forloop.Counter` and the parentloop by `forloop.Parentloop`.
 * **now**: takes Go's time format (see **date** and **time**-filter).

### Misc

 * **not in-operator**: You can check whether a map/struct/string contains a key/field/substring by using the in-operator (or the negation of it):
    `{% if key in map %}Key is in map{% else %}Key not in map{% endif %}` or `{% if !(key in map) %}Key is NOT in map{% else %}Key is in map{% endif %}`.

# Add-ons, libraries and helpers

## Official

 * [ponginae](https://github.com/flosch/ponginae) - A web-framework for Go (using pongo2).
 * [pongo2-addons](https://github.com/flosch/pongo2-addons) - Official additional filters/tags for pongo2 (for example a **markdown**-filter). They are in their own repository because they're relying on 3rd-party-libraries.

## 3rd-party

 * [beego-pongo2](https://github.com/oal/beego-pongo2) - A tiny little helper for using Pongo2 with [Beego](https://github.com/astaxie/beego).
 * [macaron-pongo2](https://github.com/macaron-contrib/pongo2) - pongo2 support for [Macaron](https://github.com/Unknwon/macaron), a modular web framework.
 * [ginpongo2](https://github.com/ngerakines/ginpongo2) - middleware for [gin](github.com/gin-gonic/gin) to use pongo2 templates

Please add your project to this list and send me a pull request when you've developed something nice for pongo2.

# API-usage examples

Please see the documentation for a full list of provided API methods.

## A tiny example (template string)

```Go
// Compile the template first (i. e. creating the AST)
tpl, err := pongo2.FromString("Hello {{ name|capfirst }}!")
if err != nil {
	panic(err)
}
// Now you can render the template with the given 
// pongo2.Context how often you want to.
out, err := tpl.Execute(pongo2.Context{"name": "florian"})
if err != nil {
	panic(err)
}
fmt.Println(out) // Output: Hello Florian!
```

## Example server-usage (template file)

```Go
package main

import (
	"github.com/flosch/pongo2"
	"net/http"
)

// Pre-compiling the templates at application startup using the
// little Must()-helper function (Must() will panic if FromFile()
// or FromString() will return with an error - that's it).
// It's faster to pre-compile it anywhere at startup and only
// execute the template later.
var tplExample = pongo2.Must(pongo2.FromFile("example.html"))

func examplePage(w http.ResponseWriter, r *http.Request) {
	// Execute the template per HTTP request
	err := tplExample.ExecuteWriter(pongo2.Context{"query": r.FormValue("query")}, w)
	if err != nil {
		http.Error(w, err.Error(), http.StatusInternalServerError)
	}
}

func main() {
	http.HandleFunc("/", examplePage)
	http.ListenAndServe(":8080", nil)
}
```

# Benchmark

The benchmarks have been run on the my machine (`Intel(R) Core(TM) i7-2600 CPU @ 3.40GHz`) using the command:

    go test -bench . -cpu 1,2,4,8

All benchmarks are compiling (depends on the benchmark) and executing the `template_tests/complex.tpl` template.

The results are:

	BenchmarkExecuteComplex                    50000             57419 ns/op
	BenchmarkExecuteComplex-2                  50000             55087 ns/op
	BenchmarkExecuteComplex-4                  50000             58348 ns/op
	BenchmarkExecuteComplex-8                  50000             58805 ns/op
	BenchmarkCompileAndExecuteComplex          10000            154818 ns/op
	BenchmarkCompileAndExecuteComplex-2        10000            141209 ns/op
	BenchmarkCompileAndExecuteComplex-4        10000            153821 ns/op
	BenchmarkCompileAndExecuteComplex-8        10000            160542 ns/op
	BenchmarkParallelExecuteComplex            50000             60640 ns/op
	BenchmarkParallelExecuteComplex-2          50000             32646 ns/op
	BenchmarkParallelExecuteComplex-4         100000             21752 ns/op
	BenchmarkParallelExecuteComplex-8         100000             18713 ns/op
