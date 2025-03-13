+++
date = '2025-02-08T18:18:07+01:00'
draft = false
title = 'What Is Boot'
summary = "Discover what boot is, why it was created and how it compare to other solution"
weight = 10
+++


Boot is a bootstrapper ~~with a very inspired name~~. It aims for simplicity, high customization and multi-language support.

It was created because neither [yeoman](https://yeoman.io/) nor [cookiecutter](https://www.cookiecutter.io/) were matching what I really wanted with a bootstrapper.

- Yeoman is a great tool, it provide a simple command (`yo`) that will help you scaffold almost any project.
The installation is really simple as it's just a `npm install` and a lot of generators are available thanks to the community.

{{< callout type="error" >}}
But to create your own generator you need to write some code in javascript using the library they provide.
{{< /callout >}}

- Cookiecutter is another great tool. There are many ways to install it like `brew`, `apt` or `pip` as it's written in python.
It relies mostly on templating using a configuration file to list and prompt all the variables used by the template engine (jinja2). Even directories and sub-directories can use the variables defined in the configuration file.

{{< callout type="error" >}}
But it felt kinda hard to ensure that dependencies would always be up-to-date, using the latest version on every new project.
{{< /callout >}}


Boot is trying to fit between those two :
- the command is simple `boot`
- you can create generators with a simple configuration file
- these generators are able to run command like `npm install x` or `go get y` (depending on the plugin you installed) ensuring latest version is used for dependencies 
- you can use templating
