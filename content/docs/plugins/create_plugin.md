+++
date = '2025-02-08T18:07:56+01:00'
draft = true
title = 'Create plugin'
weight = 20
+++

## Introduction

Creating plugin is actually very easy, as the plugin system relies on WASM, you can write your plugin in almost any language.

{{< callout type="info" >}}
Boot plugin system is based on [extism](https://extism.org), even though you don't need to use their pdk (plugin development kit) we still recommend you to use it as it provide a lot of helper function to avoid managing memory by yourself.

Moreover they oftenly provide some cli tools to help you compile your plugin to wasm (they provide one for python for example).
{{< /callout >}}

Boot plugin are categorized into 4 differents types :

- cmd
- vcs
- filer
- template_engine

Each category enable different behaviour, creating a plugin means implementing those behaviours.

## CMD / VCS

As for the current implementation, both cmd and vcs types are really easy to implement :
You just have to write a function that match the name of the action (ex: `installLocalDeps`). 
This function should return a string with the command that should be run.

```typescript
export function init(): number {
  Host.outputString("git init"); // Host comes from @extism/js-pdk

  return 0; // We return 0 so that boot understand that the plugin ended correctly.
}

```

In some cases, the command need to deals with params, thoses are provided as json array in a string input by extism's golang sdk.

```typescript
export function installLocalDeps(): number {
  let installCmd = "npm i ";
  const paramString = Host.inputString(); // We read the input string thanks to extism pdk
  const params: string[] = JSON.parse(paramString); // We parse the json array

  params.forEach(param => {
    installCmd += ` ${param}`; // We loop on the array and update the install command with params
  })

  Host.outputString(installCmd); 

  return 0; // We return 0 so that boot understand that the plugin ended correctly.
}
```
## Template Engine

Template plugin are a bit more complex but they still are pretty easy. This time the method we need to implement is `applyTemplate`.

It takes a stringified json object with 2 properties: the template string and the values (variables defined in a generator) and all it has to do is output the result string.

{{<callout type="info">}}
The values can also be retrieve in the config part. But we talk more about it in the filer part.
{{</callout>}}

For example, the default template plugin that uses jinja looks like this :
```python
# We import extism and jinja
import extism
from jinja2 import Template

# We use an extism annotation, it tells extism-py that this function is exported.
@extism.plugin_fn
def applyTemplate():
    input = extism.input_json() # we get the input and parse the json it contains

    values = input["values"]

    rtemplate = Template(input["template"]) # we create jinja's template from the incoming template string

    extism.output_str(rtemplate.render(values)) # We output (through extism) the result of the template rendering
```

## Filer

Filers plugin are probably the most complex. 

The main action to implement is `createFolderStruct`. It should retrieve data from what extism references as `config`.

{{<callout type="info">}}
In extism, there are 2 ways to pass data to a plugin function **input** and **config**.

The main difference is that config is a key-value store made for data that exists across function calls.

Boot uses plugin on data that belong to a specific action (like the params to install dependencies), and the config for the rest.


{{</callout>}}

The field it need to get from the config is `folder_struct`.
It's an array of object. The object specification depends on their type : `file` or `folder`.

Thoses object always have at least a name and the type.
They, sometime can have a `spec` field, which is an object. 

If the type is `file`, the spec object will contains a `content` field with the content of the file (most probably retrieved from the result of a template engine).

On the other hand, if the type is `folder`, the spec object will contains a `children` field, which is an array of object like `folder_struct`. This enable the creation of deep complex folder structure with nested folder and subfolders.

For example, a folder structure that look like this :

{{< filetree/container >}}
  {{< filetree/folder name="content" >}}
    {{< filetree/file name="_index.md" >}}
    {{< filetree/folder name="docs" state="closed" >}}
      {{< filetree/file name="_index.md" >}}
      {{< filetree/file name="introduction.md" >}}
      {{< filetree/file name="introduction.fr.md" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
  {{< filetree/file name="hugo.toml" >}}
{{< /filetree/container >}}

Could be represented like this: 
```json
[
  {
    "name": "content",
    "type": "folder",
    "spec": {
      "children": [
        {
          "name": "_index.md",
          "type": "file"
        },
        {
          "name": "docs",
          "type": "folder"
        }
      ]
    }
  },
  {
    "name": "hugo.toml",
    "type": "file"
    "spec": {
      "content": "
      baseURL = 'https://example.org/'
      languageCode = 'en-us'
      title = 'my suoer blog using hugo'
      "
    }
  }
]
```

Let's put things together, the filer plugin has to :

- check the config data and look for the `folder_struct` field.
- the config data is a json array of object, some can be nested, therefore filer plugin has to use recursion.
- the filer plugin is different from the other : it does not return a command to be run, it creates files and folder on the host filesystem (using WASI) so it must call wasi alternative of your language filesystem method.


Putting an example here would probably make this documentation less readable and enjoyable. Yet you can still find inspiration in the code of the [official filer plugin](https://github.com/bootengine/boot-filer-plugin) we provide.
