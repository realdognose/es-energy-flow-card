# ES Energy Flow Card
There are various cards available for Homeassistant to create energy flows - but they didn't match my personal needs, so I decided to create another one. 
What first was a code-only solution has received a lot attention, whenever I shared screenshots of my dashboard somewhere, so I went the extra mile and yamlfied everything in order to share this card with the community.

# Table of Contents
- [General](#general)
- [Configuration](#configuration) - Overview of available configuration keys.
- [Walkthrough](#walkthrough) - Defining the card step by step

### General
The card allows you to create a 100% customized layout of your home, defining expressions about your energy flow and visualize everything in realtime. 
Some impressions: 

https://github.com/user-attachments/assets/f243387a-835b-4768-b3cf-5c4e2d2b6209

Designing and configuring this card is not easy. I've added debug tools to make it as easy at possible, yet you have to have a basic understanding of fundamental maths AND yaml in order to set it up properly.
I will walk through the configuration of my view, outlining important information.

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
| TODO ||optional parameters|

### Background
This is, where it all starts. You can draw your desired schema in ANY software you prefer to use. I used - don't laugh - excel, because it offers a convinient way to add objects and rearange them as desired. 
Finally, using the snipping tool, I store the file as a regular png. 

The card is 100% automatic scaling and translating all coordinates into their scaled representation, however you achieve the best results, if you design a image-width that matches about your regular display width. 

Once you have created the background, just upload it to your Homeassistants www folder and provide the filename in the configuration: 

<img width=250 src=https://github.com/user-attachments/assets/f6338e35-d3ec-4a2f-bd89-0b2cd5f30946 />


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
| ![image](https://github.com/user-attachments/assets/b9737372-793c-44e7-a3c4-6f17a5ab8daa) | ![image](https://github.com/user-attachments/assets/2ceedb9b-6088-41d5-8356-3b998e3f83c5) | ![image](https://github.com/user-attachments/assets/b362c06f-9038-42b6-982c-ea4c9ef4c138) | ![image](https://github.com/user-attachments/assets/866cb46f-7cc9-4180-b161-703a57738e01)


### Inputs
Inputs basically map the Homeassistant sensor values to a artificial variable name that can be used in the `expressions`, `flows` and `labels section`. You can add as many inputs as you like / need, consider to give them a 
short, but meaningfull name to be able to easily reference them, when required: 

format: `${name} = sensor.unique_id`
```
inputs:
  - $Grid_L1 = sensor.grid_meter_em540_l1
  - $Grid_L2 = sensor.grid_meter_em540_l2
  - $Grid_L3 = sensor.grid_meter_em540_l3
  - $Symo_L1 = sensor.fronius_symo_l1
  - $Symo_L2 = sensor.fronius_symo_l2
  - $Symo_L3 = sensor.fronius_symo_l3
  - $Symo_S1 = sensor.solar_dach_vorne
  - $Symo_S2 = sensor.solar_dach_hinten
  - $Multi_L1_OUT = sensor.multiplus_l1_ac_out
  - $Multi_L2_OUT = sensor.multiplus_l2_ac_out
  - $Multi_L3_OUT = sensor.multiplus_l3_ac_out
```

### Expressions
Every expression you provide is evaluated with the regular javascript engine. hence every legit javascript code is a `valid` expression. 
> :warning: NOTE: Variable values can become negative. So, when specifying an expression like `$x-$y`, the final result could be `5--6` - which is invalid javascript. Make sure you have a space around the `minus` sign, to prevent this from happening: `$x - $y` would become `5 - -6` which is VALID.

Some example expressions. Within an expression, you can re-use any variable defined as `input` or of any earlier expression: 

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

Expressions can also be specified in a [flow](#flows) directly. The expression section is mainly usefull to do some calculations that you want to reuse later.

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

where with `labelPlacement` and the options `l` or `r` (default), you can control, if the name of the poi should be rendered on the left or right side, when debugging: 

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
  - 73,74,$BAT2 = $Bat_DC / 4.0
  - 77,78,$BAT3 = $Bat_DC / 4.0
  - 81,83,$BAT4 = $Bat_DC / 4.0,r
```
> :warning: You can use a simple output of an already defined variable. However, If you need to use a variable Twice, you need to assign the flow to a `new` copy of that variable. (It's defining the flows unique id)

In the expression, you can re-use any variable defined as [input](#inputs), [expression](#expressions) or defined in a flow before.
To properly design flow expressions, also consider reading the [basic maths)(#basicmaths) section. Following some simple rules will allow you to properly resolve any flow value.

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
  - 46,558,$MPPT_1 W
  - 363,555,$MPPT_2 W
  - 147,555,$Multi_L1_DC W
  - 256,555,$Multi_L2_DC W
  - 470,555,$Multi_L3_DC W
  - 44,611,$SingleBatAbs W
  - 189,611,$SingleBatAbs W,transform:translateX(-100%)
  - 324,611,$SingleBatAbs W
  - 469,611,$SingleBatAbs W
  - 145,370,$Multi_L1_IN W,transform:translateX(-100%)
  - 263,370,$Multi_L2_IN W,transform:translateX(-100%)
  - 458,370,$Multi_L3_IN W,transform:translateX(-100%)
  - >-
    5,417,$MPPT_1 W,width:70px; height:13px; font-size:14px; text-align:center;
    background-color:#8ec12d; color:black; line-height:15px; border:1px outset
  - >-
    318,415,$MPPT_2 W,width:70px; height:13px; font-size:14px;
    text-align:center; background-color:#8ec12d; color:black; line-height:15px;
    border:1px outset
  - >-
    200,610,$BatterySoC % | $Bat_DC W,width:113px; height:13px; font-size:14px;
    text-align:center; background-color:#8ec12d; color:black; line-height:15px;
    border:1px outset; font-weight:bold
```

> ℹ️ The `transform:translateX(-100%)` is quite handy, when you want to align a label to the left of an object, automatically scaling based on the final text-width.


![image](https://github.com/user-attachments/assets/9cdf741d-f5f7-40a0-b8a8-654276b63d5c)

