# Dev Guide - Power Fx Language -
## Power Apps Development

Notes for App Development Microsoft Power Apps with  **Power Fx**. Version **March 26, 2023**

## Overview
[Power Fx Overview](https://learn.microsoft.com/es-es/power-platform/power-fx/overview)

## GitHub Repo
[Power Fx Examples](https://github.com/microsoft/power-fx)

# Power Fx

## Introduction

Power Fx is the low-code language that will be used across Microsoft Power Platform. It's a general-purpose, strong-typed, declarative, and functional programming language.

Can be used like xcel-like formula bar or Visual Studio Code text window.


## Not object-oriented
`Excel isn't object-oriented, and neither is Power Fx. `

# Examples
## Set Global Vars
Storing an email address available in a global enviroment.
```yaml
Set(varEmail, inputEmailUser.Selected.'email usuario');
```
## Set Local Vars
Storing a boolean value in a local variable that can be available in the current screen.
```yaml
UpdateContext({editRecord: true});
```
