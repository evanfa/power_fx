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

Can be used like excel-like formula bar or Visual Studio Code text window.


## Not object-oriented
`Excel isn't object-oriented, and neither is Power Fx. `

# Code Use - Examples
## Set Global Vars
Storing an email address available in a global enviroment. In this case set the var from a combo box selection called `user_email`.
```yaml
Set(varEmail, inputEmailUser.Selected.'user_email');
```
## Set Local Vars
Storing a boolean value in a local variable that can be available in the current screen.
```yaml
UpdateContext({editRecord: true});
```
## Conditional IF
Consider that this is not a OO Languaje with this in mind the correct use of conditional if is like using a `function: If(condition, true, false)`

Example, consider that you want to identify if the input field `email_user` exist in a table called `users_table` with the column name `email_usr` (Is not blank the callback value).

Consider the user of `LookUp` function using it like: `LookUp(data_source,condition,result)`

```yaml
If(
    !IsBlank(
LookUp(users_table,
'email_usr'=inputEmailUser.Selected.'email_user',
'email_usr'
    )
),
    UpdateContext({validatedEmail: true});
,
    UpdateContext({validatedEmail: false});
)

```