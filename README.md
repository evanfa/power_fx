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
# Behavior Functions
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
## More actions
Use semicolons to create a list of actions to perform. For example, you might want to update a context variable and then return to the previous screen, in this case a local var is setted and a behavior action return to the previous screen:

```yaml
UpdateContext( { x: 1 } ); Back()
```

## Notify Function
The Notify function displays a banner message to the user at the top of the screen. The notification will remain until the user dismisses it or the timeout expires which defaults to 10 seconds.

Sintaxis: `Notify(Message , NotificationType , Timeout)`

```yaml
Notify( "Hello, World" )
Notify( "Hello, World", NotificationType.Error )
Notify( "Hello, World", NotificationType.Warning, 4000 )
```

## Count
The Count functions work exactly than excel. Retrieve the total rows identified in a data set where the logical condition is true.
### Function Count
`Count(column,logical)`

## Switch
```yaml
Switch(
        _censusTypeFastSearch;
        "byIdPermit";
        Filter(
                pmas_permits;
                id_permit = Trim(InputPermitId.Text);
                id_project = _censusCurrentProject 
            );
        "byNamePermit";
        Filter(
                pmas_permits;
                name_permit = Trim(InputPermitName.Text);
                id_project = _censusCurrentProject
            );
        "byState";
        Filter(
                pmas_permits;
                permit_state = ListStates.SelectedText.Value And id_project = _censusCurrentProject
            );
        "byMunicipality";
        Filter(
                pmas_permits;
                permit_state = ListMunicipality.SelectedText.Value And id_project = _censusCurrentProject
            );
        (
            Filter(
                    pmas_permits;
                    id_project>0
                )
            )
    )
```

# If and ForAll Function
```yaml
If(
    CountRows(Filter(pmas_agent_to_permits; id_permit = _currentIdPermit)) = 0;
        /*True*/
        ForAll(
            AgentAssignments;
            If(IsBlank(Value(
                LookUp(pmas_agents;name_usr = AgentAssignments[@Value]).id_agent));
                    Patch(
                        pmas_agent_to_permits;
                        {
                            id_permit: _currentIdPermit;
                            id_agent: Value(
                                LookUp(
                                    pmas_agents;
                                    name_usr = AgentAssignments[@Value]
                                ).id_agent)
                        }
                    )
            )
        )
)
```

# For All Function
```yaml
ForAll(
    AgentAssignments;
    If(IsBlank(Value(
        LookUp(pmas_agents;name_usr = AgentAssignments[@Value]).id_agent)) And 
        CountRows(Filter(pmas_agent_to_permits; id_permit = _currentIdPermit)) = 0;
        /*True*/
            Patch(
                pmas_agent_to_permits;
                {
                    id_permit: _currentIdPermit;
                    id_agent: Value(
                        LookUp(
                            pmas_agents;
                            name_usr = AgentAssignments[@Value]
                        ).id_agent)
                }
            )
    )
);;
```

Other example:
```yaml
ForAll(
    pmas_trans_to_agents;
    Collect(
        DetailsUpdatesByAgent;
        {
            agent_id: LookUp(
                pmas_agents;
                id_agent = pmas_trans_to_agents[@farl_ids_agents]
            ).id_agent;
            agent_name: LookUp(
                pmas_agents;
                id_agent = pmas_trans_to_agents[@farl_ids_agents]
            ).name_usr
        }
    )
);;
```

# ClearCollect and Collect
```yaml
ClearCollect(
    DetailsUpdatesByAgent;
    {}
);;

ClearCollect(
   DetailsUpdatesByAgent;
   AddColumns(
       pmas_transactions;"id_transaction";LookUp(pmas_trans_to_agents;Text(id_transaction) = pmas_transactions[@idTransaction])
   )
);;
```

# Validate if a record is inside a table and if not add new one
```yaml
Collect(
    CollectAgentOfSelectedPermit;
    {
        agent_id: LookUp(
            pmas_agents;
            email_user = "" & ThisItem.pmas_user_email
        ).id_agent;
        agent_name: LookUp(
            pmas_agents;
            email_user = "" & ThisItem.pmas_user_email
        ).name_usr;
        agent_rol: ThisItem.pmas_user_rol;
        agent_email: ThisItem.pmas_user_email
    }
);;
```
```yaml
If(
    GalleryAgentsAddition.AllItemsCount > 0;
    ForAll(
        Filter(CollectAgentOfSelectedPermit;!IsBlank(agent_name));
        If(
               IsBlank( LookUp(
                    pmas_agent_to_permits;
                    id_permit = _currentTempPermitToAssignExist && id_agent = agent_id
                ));
            Patch(
                pmas_agent_to_permits;
                Defaults(pmas_agent_to_permits);
                {
                    id_permit: _currentTempPermitToAssignExist;
                    id_agent: agent_id//ThisRecord.agent_id
                }
            )
        )
    )
);;
```