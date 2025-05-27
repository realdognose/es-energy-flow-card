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

### Inputs

### Expressions

### Pois

### Flows

### Labels
