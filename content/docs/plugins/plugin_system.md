+++
date = '2025-02-08T18:08:42+01:00'
draft = false
title = 'Plugin system'
weight = 10
summary = 'Plugins are the best way to tune your boot instance to your own needs. Learn how they are integrated to boot to get the best of it.'
+++

The plugin system is a very important part of `boot`. It's what make `boot` able to generate project for any language and it's also what makes `boot` able to be personallized with almost any language.

Even if the concept is simple, there still are a few things to explain or details.

1) The plugin are installed locally. The idea is that, if you don't need to deal with dependencies or if your packagemanager provide cache functionnality (like [pnpm](https://pnpm.io/) for example), then `boot` will be able to run offline.

2) All the installed plugin (even the pre-installed one) can be removed or have their path changed. We provide default module so that you can start with `boot` as fast as possible, we do not force you to use them.

3) Plugin comes in 4 differents types giving them different capabilities (actions) : VCS, Cmd, Filer and Template Engine.


|Plugin type| Actions|
|---|---|
|vcs| init, add, addOrigin, commit, push|
|cmd| init, installLocalDeps, installGlobalDeps, installDevDeps|
|filer| createFolderStruct|
|template_engine| applyTemplate|

- The vcs kind of plugin is for anything like git, svn, ...
- The cmd kind of plugin is for tools like package manager like npm, cargo, yarn, composer, ...
- The filer kind of plugin is for filesystem operation.
- The template_engine kind of plugin is for templating like jinja, ejs, pug, go template, ...

{{< callout type="info" >}}
  The plugin system relies on [extism](https://extism.org/), you should give it a look as they provide a lot of tools to help you create plugins in wasm, with a lot of supported languages.
{{< /callout >}}


