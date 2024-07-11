# Plug: A CLI for nih-plug

This is an idea for a cli application that deals with creating projects from templates contained inside a git repository 
and managing workspace members. 

## Considerations

An nih-plug project typically consists of a workspace and the actual plugin code. These can either be in the same
workspace or split across multiple projects. For a single plugin project, everything can be contained within the
same `Cargo.toml` file, as demonstrated by the
official [cookie-cutter template](https://github.com/robbert-vdh/nih-plug-template/blob/master/%7B%7B%20cookiecutter.project_name%20%7D%7D/Cargo.toml).
Alternatively, for plugin collections the workspace can contain any number of subprojects containing the plugin code. If
developing a library alongside the plugin collection the workspace can also contain a library that is not a member,
such as in [valib](https://github.com/SolarLiner/valib/blob/main/Cargo.toml)
or [nih-plug](https://github.com/robbert-vdh/nih-plug).

Subprojects can be located in any directory but must be registered in the `members` section of the main
project's `Cargo.toml`. This file is crucial for any nih-plug project and is the only hard requirement (Of course,
xtask is also required to build anything).

The subprojects also contain important meta-information about the plugin, such as the plugin's name, type (e.g., audio
effect, synthesizer), the number of audio inputs and outputs, and the target formats (e.g., VST3, CLAP, standalone).
This information is typically stored in the `lib.rs` file of the project. Since editing this data is better suited to an
IDE than a command-line interface, this application does not handle such edits.

**The scope of this application is to provide a CLI to:**

* Create workspaces and projects
* Modify the workspaces `Cargo.toml` by adding or removing subprojects

## Overview of Plug's Repositories

Plug should ultimately reside in these two Git repositories:

* **Plug Binary Repository**: Contains the CLI project and its source code.
* **Plug Template Repository**: Contains templates for creating various projects. Custom projects can also be supported
  by using a different repository.

## Usage of Template Repositories by Plug

The template repository should be checked out into a folder owned by Plug. From there the templates are copied to the 
location when created.

# Tasks

## Create
`plug create repo_name:template_path output_path`

Will create a workspace from template containing using the given `template_path` inside the internal Plug 
template folder. If no repo_name was provided it will take the default repo.

Will create a workspace or a subproject from template using the given `template_path` inside the internal Plug 
template folder. If no repo_name was provided it will take the default repo. If the project is a subproject `add` task 
is automatically called. If output_path is not empty it will give an option to create it as a library in the main 
workspace.

**Example**

`plug create "workspace/default" output_path`
`plug create "plugin/gain_vizia" output_path`

## Workspace

Actually a duplicate of: [cargo-workspaces](https://github.com/pksunkara/cargo-workspaces/tree/master)

`plug workspace add subproject_path`

`plug workspace remove subproject_path`

Add or remove project to or from the workspaces `Cargo.toml`. The plugin should be in a local path and
is specified by `subproject_path`.

**Example**

`plug add my_plugin`

`plug remove my_plugin`

## Build

`plug bundle [project_name] [--release]`

Builds the project `project_name` and produces the desired formats.
If `project_name` is omitted the project is derived from the working directory.
With `--release` the project is build in release mode (same as in Cargo) otherwise in debug mode.

## Repo

`plug repo add git_repo`

`plug repo remove git_repo`

`plug repo update [git_repo]`

Plug add will check out a new repo from the provided url into the internal Plug template folder, while remove will delete
the folder. The update command will update the provided repo or if no repo name is provided it will update all. 


