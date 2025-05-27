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

### Indicator

### Debug
The debug-parameter is a very important feature, when doing your configuration. The available modes are `pois`, `flows` and `both`. When debugging, the following actions will be performed: 
- The whole card is slightly blurred out to make things better readable.
- Any defined labels are not rendered.
- The requested debug parameters are added to the card.

Bellow you can see 4 examples of either mode: 

`both` can become very crowdy, so during configuration it is crucial to use the additional debug-info-positioning parameters, as explained in the [pois](#pois) and [flows](#flows) section.

| Regular Mode | pois | flows | both |
|-------------|-------|-------|------|
| ![image](https://github.com/user-attachments/assets/b9737372-793c-44e7-a3c4-6f17a5ab8daa) | ![image](https://github.com/user-attachments/assets/2ceedb9b-6088-41d5-8356-3b998e3f83c5) | ![image](https://github.com/user-attachments/assets/b362c06f-9038-42b6-982c-ea4c9ef4c138) | ![image](https://github.com/user-attachments/assets/8d11d0af-0692-409e-9ae0-5beb37c316f2) |

### Inputs

### Expressions

### Pois

### Flows

### Labels
