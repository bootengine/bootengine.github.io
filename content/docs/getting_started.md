+++
date = '2025-02-08T18:07:42+01:00'
draft = true
title = 'Getting started'
weight = 40
+++

## Create the generator file

Now that boot is installed you can just run it without any args see the help menu with all available commands.


The one that we'll see first is the following replacing [generator-name] by whatever you want to call your generator:

{{< callout type="info" >}}
  We recommand either creating this file in a dedicated directory or prefixing the name with `boot-` just to not get confused with any other yaml file.

Moreover, the name of the file should reflect the kind of application it will generate (ie: `boot-express-api.yaml`).
{{< /callout >}}

```bash
boot init -o [generator-name].yaml
```

This command will create a file with default options enabled. This should look like this :

```yaml {filename="boot-express-api.yaml" linenos=table}
config:
    create_root: true
    unrestricted: false
vars:
    - name: project_name
      type: string
      required: true
    - name: license
      type: license
      required: true
steps:
    - name: git init
      module: git
      action: init
    - name: license
      module: license
    - name: create folder structure
      module: filer
      action: createFolderStruct
folder_struct:
    - .gitignore
    - README.md
```


## How does a generator file works ?

As you can see in the previously generated file, a generator file is a config file (yaml, json, ...)
that come with 4 main parts

### Config

The `config` part details how `boot` will deal with the generator.

It comes with very few options :

| option | behaviour |
|---|---|
|create_root | this options tells `boot` if it need to create the directory for your project. if set to true, the default context directory used for Steps will be the one created. If set to false, the default context will bethe current working directory. |
| unrestricted | `boot` multilanguage support come from the plugin system. Each plugin returns a set of command that `boot` will execute. We added a small security check because we think a bootstrapper should not need to run `sudo` or `rm` based command. If you want to disable this check, just set this option to `false`  |

{{< callout type="warning" >}}
  `boot` dev team is not responsible for the plugin you install/use. We recommand you to be careful and check the code or the compiled `.wasm` file if possible. In the meantime, `boot` was made so that plugin creation is easy and supported in many language (as long as it compile to wasm), so remember that you can always create your own plugin.
{{< /callout >}}


### Vars

Vars are data that change from one projecct to another. Each variable is prompted by `boot` at generation time.
They can be `required` or not. If they are, `boot` will errors out if no value were provided.

vars can have a type which is one of the following `string` | `license` | `password`.

{{< callout type="info" >}}
  Vars are always prompted in the order they are defined and are passed along every plugin and can be used inside templates.
{{< /callout >}}

### Steps

Steps is where "the magic happens" ! Well there's no magie here but it's where you define which action the generator will perform and in which order. It basically works like a task runner. 

For each step, you can provide a name (for logging purpose), a module that will be invoked, an action to perform, and optionnally you can also provide some parameters and a directory in which the action will be performed.


{{< callout type="info" >}}
  `boot` is battery included, it comes with 3 plugins installed folder structure creation, the templating and git commands.
  But it also includes an embed module for the licenses.

The default template plugin is using [Jinja](https://jinja.palletsprojects.com/en/stable/).
{{< /callout >}}
 

### Folder structure

The folder structure part is where you define the shape of the generated application.
It's an array of files and folder that can be recursive. Files can be based on templates.
The templates are defined with a given template_engine and a path to the template file.
This pattern gives you the possibility to :
- reuse template files in many generator
- use as many template engine you want for your file generation.

{{< callout type="info" >}}
  We recommand you to store your template file in a dedicated folder.
  It will be easier for you to manage them.
{{< /callout >}}

## Checking integrity


Now that we know what our generator is made about, we can check that it is a valid one with a simple command :

```bash
boot check -f boot-express-api.yaml
```


## Running our generator

```bash
boot gen -f boot-express-api.yaml
```
