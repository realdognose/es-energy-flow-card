# ES Energy Flow Card
There are various cards available for Homeassistant to create energy flows - but they didn't match my personal needs, so I decided to create another one. 
What first was a code-only solution has received a lot attention, whenever I shared screenshots of my dashboard somewhere, so I went the extra mile and yamlfied everything in order to share this card with the community.

# Table of Contents
- [General](#general)
- [Configuration](#configuration) - Overview of available configuration keys.
  - [Background](#background)
  - [Indicator](#indicator)
  - [Debug](#debug)
  - [Inputs](#inputs)
  - [Expressions](#expressions)
  - [POIs](#pois)
  - [Flows](#flows)
  - [Labels](#labels)
- [Basic Maths](#basic-maths) - tiny cook book to resolve flows.
- [Walkthrough](#walkthrough) - Defining the card step by step

## General
The card allows you to create a 100% customized layout of your home, defining expressions about your energy flow and visualize everything in realtime. 
Some impressions (old and new layout of my system): 

https://github.com/user-attachments/assets/d3c8371a-3f29-405c-8fd8-7cba1e48892e

https://github.com/user-attachments/assets/18a355bd-3743-418f-8292-29851cb2cd55

Designing and configuring this card is not easy. I've added debug tools to make it as easy at possible, yet you have to have a basic understanding of fundamental maths AND yaml in order to set it up properly.
I will walk through the configuration of my view, outlining important information.

I'm a software developer, so didn't use any fancy graphic design tools, but excel and MS Paint to create the basic layout.

## Configuration

The card offers various options to configure the look and feel. Bellow is a summery of the available keys and their possible values. If you find a `Read More` link in the table, you can jump to the section
explaining the respective key in detail.

| Key | Possible Values | Short Description | Long Description |
| ------------------------|---------------------|-------------------------------------------------------------------------------------------------------|--|
| type                  | es-energy-flow-card             | Type of the card | |
| background            | filename                        | Filename of your backgroundfile stored in the www folder of homeassistant. | [Read More](#background) |
| indicator             | filename                        | Filename of the indicator to be used, stored in the www folder of homeassistant. | [Read More](#indicator) |
| debug                 | pois, flows, both               | Selects the desired debug mode. Any string is valid, only the 3 mentioned options have an impact | [Read More](#debug) |
| inputs                | *                               | Mapping of input variables | [Read More](#inputs) |
| expressions           | *                               | Additional expressions to be evaluated | [Read More](#expressions) |
| pois                  | *                               | Points-of-interest. Any flow start/end and intersection should be a poi | [Read More](#pois) |
| flows                 | *                               | Energy flows to render | [Read More](#flows) |
| labels                | *                               | Labels to show and update | [Read More](#labels) |
| laneWidth | * | width a lane should have in px ||
| minSize | * | minimum size of a energy-bubble in px ||
| maxSize | * | maximum size of a energy-bubble in px ||
| minMax | * | smallest maximum value to scale against (Watts) ||
| maxMax | * | largest maximum value to scale against (Watts) ||


### Background
This is, where it all starts. You can draw your desired schema in ANY software you prefer to use. I used - don't laugh - excel, because it offers a convinient way to add objects and rearange them as desired. 
Finally, using the snipping tool, I store the file as a regular png. 

The card is 100% automatic scaling and translating all coordinates into their scaled representation, however you achieve the best results, if you design a image-width that matches about your regular display width. 

Once you have created the background, just upload it to your Homeassistants www folder and provide the filename in the configuration: 

I've used excel to create the basic image, using column and row settings of 9x9 pixels served the needs I had. 

<img width="235" alt="Victron-BG-2026-10-10_2" src="https://github.com/user-attachments/assets/2244d491-5275-4661-856a-83956621a637" />

I've placed a marker bottom right, so whenever I'm gonna "snip" the image, I can ensure the final png
(pasted in paint) can be adjusted to the very same width as before, so the flows do match again. 

<img width="235" alt="image" src="https://github.com/user-attachments/assets/d7b81061-a855-42b8-8b21-de72f3aaef82" />


```
background: MyFileName.png
```
### Indicator
The indicator is the representation of a single energy-bubble floating around. You can use any file desired, heres the one, I decided to use: 

![flowCircle](https://github.com/user-attachments/assets/c86314c5-91f9-4102-8f07-98f9064b564b)

```
indicator: flowCircle.png
```

### Debug
The debug-parameter is a very important feature, when doing your configuration. The available modes are `pois`, `flows` and `both`. When debugging, the following actions will be performed: 
- The whole card is slightly blurred out to make things better readable.
- Any defined labels are not rendered.
- The requested debug parameters are added to the card.

Bellow you can see 4 examples of either mode: 

`both` can become very crowdy, so during configuration it is crucial to use the additional debug-info-positioning parameters, as explained in the [pois](#pois) and [flows](#flows) section.

| Regular Mode | pois | flows | both |
|-------------|-------|-------|------|
| <img alt="regular" src="https://github.com/user-attachments/assets/2eb8b0d2-cd9c-4d26-b7a0-efbf4cf9d3c7" /> | <img alt="pois" src="https://github.com/user-attachments/assets/225efc9a-3035-4e3b-91ae-f6798933ecfd" /> | <img alt="flows" src="https://github.com/user-attachments/assets/dc4b53f6-9f28-499c-a7b3-5df4faaea295" /> | <img alt="both" src="https://github.com/user-attachments/assets/445e8031-9f7d-4242-8fd3-1fcaf05bbaac" /> |
 


### Inputs
Inputs basically map the Homeassistant sensor values to a artificial variable name that can be used in the `expressions`, `flows` and `labels section`. You can add as many inputs as you like / need, consider to give them a 
short, but meaningfull name to be able to easily reference them, when required: 

format: `${name} = sensor.unique_id`
```
inputs:
  - $Grid_L1 = sensor.grid_meter_em540_l1
  - $Grid_L2 = sensor.grid_meter_em540_l2
  - $Grid_L3 = sensor.grid_meter_em540_l3
  - $Multi_L1_OUT = sensor.multiplus_l1_ac_out
  - $Multi_L2_OUT = sensor.multiplus_l2_ac_out
  - $Multi_L3_OUT = sensor.multiplus_l3_ac_out
  - $EV_L1r = sensor.l1_power
  - $EV_L2r = sensor.l2_power
  - $EV_L3r = sensor.l3_power
  ...
```

### Expressions
Every expression you provide is evaluated with the regular javascript engine. hence every legit javascript code is a `valid` expression. 
> :warning: NOTE: Variable values can become negative. So, when specifying an expression like `$x-$y`, the final result could be `5--6` - which is invalid javascript. Make sure you have a space around the `minus` sign, to prevent this from happening: `$x - $y` would become `5 - -6` which is VALID.

Some example expressions. Within an expression, you can re-use any variable defined as `input` or of any earlier expression. Expressions are evaluated in order of listing.: 

```
expressions:
  - $Multi_L1_IN = $Grid_L1
  - $Multi_L2_IN = $Grid_L2
  - $Multi_L3_IN = $Grid_L3
  - $Multi_L1_DIFF = $Multi_L1_OUT - $Multi_L1_IN
  - $Multi_L2_DIFF = $Multi_L2_OUT - $Multi_L2_IN
  - $Multi_L3_DIFF = $Multi_L3_OUT - $Multi_L3_IN
  - $Multi_DIFF = $Multi_L1_DIFF + $Multi_L2_DIFF + $Multi_L3_DIFF
  - $SingleBatAbs = Math.abs($Bat_DC/4.0)
  - $GridTotal = Math.round($Grid_L1 + $Grid_L2 + $Grid_L3)
```

Expressions can also be specified in a [flow](#flows) directly. The expression section is mainly usefull to do some calculations that you want to reuse later but are not directly tied to a certain flow.

### Pois
Pois specify where a flow starts and ends. ANY intersection of your schema should be considered a `poi` because that's where energy flows are forked or joined. 
Pois are best created by opening your image file in paint, and review (bottom left) the coordinates of where your mouse-coursor is pointing. 

Some basic rules to make "good pois": 
| | |
|------|------|
| Align them, where the flow is supposed to start and end. (The purple dot is NOT required, that is where my cursor is pointing at) | ![image](https://github.com/user-attachments/assets/f8b15a68-613b-4747-a1df-cfac6fc2e60e) |
| When you create pois "in-line" make sure they use the same `x` respective `y` coordinate.| ![image](https://github.com/user-attachments/assets/6e803ce8-b425-4af9-b7e4-11d37e8230d9) |
| At intersections, pick the poi to be centered. | ![image](https://github.com/user-attachments/assets/4278ce2b-e599-40a5-bce9-84a3a3911790) |

the basic notation of creating a poi is 
```
  - id,x,y,labelPlacement
```

where with `labelPlacement` and the options `l`, `r` (default), `t` and `b`, you can control, if the name of the poi should be rendered on the left, right, top or bottom side, when debugging: 

example pois: 
```
pois:
  ...
  - 2,56,140,l
  - 3,99,140
  - 4,72,170,l
  ...
```

![image](https://github.com/user-attachments/assets/f962ed7e-fae5-4534-8888-63db0565ed57)

### Flows
Flows are the heart of the card. A flow is directed from one `poi` to another and specifying an expression about it's value. 

the basic notation of creating a flow is 
```
  - poi_from,poi_to,expression,labelPlacement
```
where with `labelPlacement` and the options `t`, `b`, `r` or `l` (default), you can control, if the debug information should be rendered on the top, bottom, left or right side, when debugging: 

example flows:
```
flows:
  - 55,56,$MPPT_1,l
  - 66,67,$MPPT_2,r
  - 68,69,$MPPT_1_DC = $MPPT_1,l
  - 80,79,$MPPT_2_DC = $MPPT_2,r
  - 72,71,$Multi_L1_DC
  - 76,75,$Multi_L2_DC
  - 82,81,$Multi_L3_DC,r
  - 69,70,$BAT1 = $Bat_DC / 4.0,l
  - 73,74,$BAT2 = $Bat_DC / 4.0,
  - 77,78,$BAT3 = $Bat_DC / 4.0,
  - 81,83,$BAT4 = $Bat_DC / 4.0,r
```
> ℹ️ Note, that omitting the position is not the same as `,` for flows. A flow without a value for the position parameter will use the default. A flow with a empty value provided will not have a rendered label during debug view. (Usefull in congested areas with many micro-flows)

> :warning: You can use a simple output of an already defined variable. However, If you need to use a variable Twice, you need to assign the flow to a `new` copy of that variable. (It's defining the flows unique id)

In the expression, you can re-use any variable defined as [input](#inputs), [expression](#expressions) or defined in a flow before.
To properly design flow expressions, also consider reading the [basic maths](#basic-maths) section. Following some simple rules will allow you to properly resolve any flow value.

![image](https://github.com/user-attachments/assets/df2bc49c-0491-4050-9255-6d83efe241e0)

### Labels
Labels are there to add readable information to the flow.

the basic notation of creating a label is 
```
  - x,y,label text, css style
```

example labels:
```
labels:
  - 123,770,$Bat_1_Temp:1 °C,transform:translateX(-100%)
  - 183,770,$Bat_2_Temp:1 °C,transform:translateX(-100%)
  - 429,770,$Bat_3_Temp:1 °C,transform:translateX(-100%)
  - 524,770,$Bat_4_Temp:1 °C,
  - 362,622,$Multi_1_Temp:1 °C,
  - 470,622,$Multi_2_Temp:1 °C,
  - 578,622,$Multi_3_Temp:1 °C,
  - 236,622,$MPPT_RS_Temp:1 °C,transform:translateX(-100%)
  - 146,622,$MPPT_2_Temp:1 °C,
  - 50,622,$MPPT_1_Temp:1 °C,
  - >-
    310,603,$Multi_L1_DC W | $Multi_L1_A A,width:83px; height:12px;
    font-size:10px; text-align:center; background-color:#8ec12d; color:black;
    line-height:13px; border:1px outset
  - >-
    420,603,$Multi_L2_DC W | $Multi_L2_A A,width:83px; height:12px;
    font-size:10px; text-align:center; background-color:#8ec12d; color:black;
    line-height:13px; border:1px outset
  - >-
    530,603,$Multi_L3_DC W | $Multi_L3_A A,width:83px; height:12px;
    font-size:10px; text-align:center; background-color:#8ec12d; color:black;
    line-height:13px; border:1px outset
  - >-
    205,603,$MPPT_RS W | $MPPT_RS_A A,width:78px; height:12px; font-size:10px;
    text-align:center; background-color:#8ec12d; color:black; line-height:13px;
    border:1px outset
  - >-
    200,678,$MPPT_ALL W,width:48px; height:12px; font-size:10px;
    text-align:center; background-color:#8ec12d; color:black; line-height:13px;
    border:1px outset
  - >-
    200,703,$MPPTs_A:1 A,width:48px; height:12px; font-size:10px;
    text-align:center; background-color:#8ec12d; color:black; line-height:13px;
    border:1px outset
  - >-
    340,678,$Bat_DC W,width:58px; height:12px; font-size:10px;
    text-align:center; background-color:#8ec12d; color:black; line-height:13px;
    border:1px outset
  - >-
    340,703,$Bat_A:1 A,width:58px; height:12px; font-size:10px;
    text-align:center; background-color:#8ec12d; color:black; line-height:13px;
    border:1px outset
  - >-
    210,763,$BatterySoC:1 % | $Bat_DC W | $TTG,width:190px; height:13px;
    font-size:14px; text-align:center; background-color:#8ec12d; color:black;
    line-height:15px; border:1px outset; font-weight:bold
  - >-
    195,417,$MPPT_RS_S1 W,width:42px; height:13px; font-size:10px;
    text-align:center; background-color:#8ec12d; color:black; line-height:15px;
    border:1px outset
  - >-
    251,417,$MPPT_RS_S2 W,width:42px; height:13px; font-size:10px;
    text-align:center; background-color:#8ec12d; color:black; line-height:15px;
    border:1px outset
  - >-
    15,425,$MPPT_1 W,width:60px; height:13px; font-size:14px; text-align:center;
    background-color:#8ec12d; color:black; line-height:15px; border:1px outset
  - >-
    107,425,$MPPT_2 W,width:60px; height:13px; font-size:14px;
    text-align:center; background-color:#8ec12d; color:black; line-height:15px;
    border:1px outset
```

in the label text, you can specify any variable defined/calculated so far.

> ℹ️ The `transform:translateX(-100%)` is quite handy, when you want to align a label to the left of an object, automatically scaling based on the final text-width.

<img width="350" alt="image" src="https://github.com/user-attachments/assets/5d74f7ad-d612-4045-a9c3-a73475266fec" />

Values inside labels are usually rounded to a 0-precision, but if needed this can be overwritten: `- 470,622,$Multi_2_Temp:1 °C,`, where `:1` would cause the value to be rounded to 1-precision.

## Basic Maths

You can resolve flows through logic, mostly. Getting the sign of the value right however is critical for the flow widget, as a negative value inverts the flows visualization. 

- If you specify a flow left-to-right, a positive value will flow to the right, a negative to the left. 
- If you specify a flow right-to-left, then a positive value will flow to the left, a negative to the right. 

The fundamental principle behind is Kirchhoffs Law, and it quite simply says: The sum of all values has to be 0. Or simplified: What comes in, has to go out. 

So, you need to work your way through all flows, starting with the trivial flows that are measured values (Inverter Values, Grid values, ...) 
Whenever you reach an intersection, you may need to resolve other flows first before you can proceed: 

- for a 3 way intersection, you need to know 2 of the connected flows already. 
- for a 4 way intersection, you need to know 3 of the connected flows already. 

Example 1: 
We know `Incoming` and `Outgoing` already. And we specify that `Third` flows away, too. Then it's expression gonna be: `$Third = $Incoming - $Outgoing`

<img width="501" height="228" alt="image" src="https://github.com/user-attachments/assets/fbc695f9-8c1d-4a77-b4a8-5267ed08bed8" />

If we would like to specify it as an incoming flow, it would be: `$Third = $Outgoing - $Incoming`

<img width="500" height="247" alt="image" src="https://github.com/user-attachments/assets/7671e937-2379-4f91-85b4-ff3a658f0658" />

Strictly applying Kirchhoffs Law would work the following way: 

- `0=` is our starting point
- `Incoming` has a positive indicator, so we add it's value to the Right: `0=+$Incoming`
- `Outgoing` has a negative indicator, so we subtract it's value from the Right `0=+$Incoming-$Outgoing`
- Taking the example of we want `Third` to be Outgoing, we subtract this value as well: `0=+$Incoming-$Outgoing-$Third`
- And finally, we just resolve for `Third`, so: `0=+$Incoming-$Outgoing-$Third <=> $Outgoing=+$Incoming-$Third <=> $Outgoing-$Incoming=-$Third <=> $Third=$Incoming-$Outgoing`
