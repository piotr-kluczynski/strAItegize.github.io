---
layout: page
title: "Simulation"
permalink: /simulation
---

# strAItegize.github.io/simulation

Welcome to the page of <b>strAItegize</b> project documentation, dedicated to the simulation app! In this document, all the program configuration, its modules, and functions will be described.

## 1. Overview
The application is a specialized program for evaluating the capabilities and behaviour of Artificial Intelligence (AI)-based decision making systems, operating on written language (English). The development of this tool is intended to address a gap in existing software for testing AI systems, enabling them to be examined in a multi-faceted environment with persistance.

The tool was designed with AI system modality in mind, which allows for experiments with various types of artificial intelligence, provided that the middleware responsible for communication meets specific requirements and provides responses defined by the simulation program.

The program code has been divided into so-called modules - sets of classes and functions that implement a specific range of functionality necessary for the application to operate. The central component of each module is the <i>Manager Module</i>, which, based on parameters specified by the user when launching the application, correctly initializes the simulation, enabling it to function properly.

## 2. Setup & Usage

Uruchamianie programu

Jak skonfigurować własny system AI aby mógł wziąć udział w symulacji?

Tworzenie/Edytowanie istniejących map

Dodawanie/Edytowanie istniejących requestów

## 3. Modules
### 3.1. Management

### 3.2. Simulation

### 3.3. Communication

### 3.4. Registering

## 4. Classes

### 4.1 GameController

### 4.2. GameLoader

### 4.3. Simulation
#### Description
Class responsible for managing the simulation, allowing for seamless proceeding between round, using methods such as start_round or add_player_orders.
Utilizes middle and low-level classes, such as Board, or Unit to provide a high-level interface to interact with its environment.

#### Properties
##### players
List of player_id's (str) used for their identification.

##### players_status
Dictionary (player_id:PlayerStatus) describing whether player still participates in the simulation.

##### players_events
Dictionary (player_id:List[str]) containing "simulation events" to display for players at the round start.

##### players_orders
Dictionary (player_id:List[UnitOrder]) containing orders given by the players to be executed at the round end.

##### units
Dictionary (unit_id:Unit) containing all units within the simulation.

##### board
Board class object, related to the current session.

##### round
Round counter, increased at the round end.

##### max_round
Round limit (integer), after which the simulation will end.

##### upkeep_round
The round frequency (integer) with which the "upkeep round" occurs, resulting in recruitment/disbanding of units.

##### type_counters
Dictionary (str:int) containing the most recent id number of recruited unit, separate for each unit type. Used to generate unique unit ids.

##### simulation_publisher
SimulationPublisher class object, allowing simulation to signal specific occurrences to be processed independently by other systems.

#### Methods
##### __init__(players, max_round, upkeep_round, tiles, regions, type_counters=None)
<b>Description: </b>
Method initializing the Simulation object, and setting up default values for its properties.

<b>Args: </b>
- players: list[str] - list of player_id's, assigned to players property,
- max_round: int - round limit, after which simulation will end, assigned to max_round property,
- tiles: dict[tuple[int, int, int], Tile] - dictionary ((q, r, s):Tile) building together the game board, used to initialize the Board object for board property,
- regions: dict[str, Region] - dictionary (region_id:Region) creating the administrative division of the game board, used to initialize the Board object for board property,
- type_counters: dict[str, int] - dictionary (str:int) containing the first available id number per unit type, assigned to the type_counters property.

<b>Returns: </b> 
None

##### observe_unit(unit_id)
<b>Description: </b>
Method returning all tiles within the range of 3 units of distance from the unit with given unit_id. Used for providing simulation information to the session participants.
<b>Args: </b>
- unit_id: str - unique identifier of the chosen unit.

<b>Returns: </b> 
tiles: list[Tile] - list of Tile class objects surrounding the unit with given unit_id.

##### observe_region(region_id)
<b>Description: </b>
Method returning all tiles belonging to the region with given region_id. Used for providing simulation information to the session participants.

<b>Args: </b>
- region_id: str - unique identifier of the chosen region.

<b>Returns: </b> 
tiles: list[Tile] - List of Tile class objects assigned to the region with given region_id.

##### get_player_units(player_id)
<b>Description: </b>
Method returning unit_id's of all units belonging to the player with given player_id. Used for providing simulation information to the session participants.

<b>Args: </b>
- player_id: str - unique identifier of the chosen player.

<b>Returns: </b> 
unit_ids: list[str] - List of unit_ids of all units owned by the player.

##### get_player_regions(player_id)
<b>Description: </b>
Method returning region_id's of all regions controlled by the player with given player_id. Used for providing simulation information to the session participants.

<b>Args: </b>
- player_id: str - unique identifier of the chosen player.

<b>Returns: </b> 
region_ids: list[str] - List of region_ids of all regions controlled by the player.

##### get_neighbouring_regions()
<b>Description: </b>
Method returning nested list of neighbouring region ids for each region within the simulation session. Used for providing simulation information to the session participants.

<b>Args: </b> None

<b>Returns: </b> 
neighbouring_regions_ids: list[list[str]] - List of region_ids for each region.


##### start_round(new_units=None)
<b>Description: </b>
High-level method initializing new round, and preparing components within the simulation. Stops the simulation if game over conditions has been met, performs the upkeep phase if needed, and removing unsuccessful participants from the simulation.

<b>Args: </b>
- new_units: dict[str, list[RecruitmentChoice]] - optional argument, by which the choice regarding unit recruitment choice is passed(only on upkeep rounds). None by default.

<b>Returns: </b> 
does_sim_cont: bool - False if simulation has come to an end, True otherwise.

##### end_round()
<b>Description: </b>
High-level method finalizing current round, and preparing the components within the simulation. Executing player's orders, resetting unit's temporary parameters, changing regions ownership, and increasing round counter.

<b>Args: </b>None

<b>Returns: </b>None

##### recruit_units(player, budget, unit_request)
<b>Description: </b>
Medium-level method creating new units, of type and in a location given by the list of RecruitmentChoice objects. 
Each request is resolved in for loop, which stops when the end of the list is reached, or the unit requests cost surpasses the budget.

<b>Args: </b>
- player: str -  unique identifier of the player recruiting the units,
- budget: int - the surplus of supplies used to recruit new units,
- unit_requests: list[RecruitmentChoice] - list of RecruitmentChoice objects containing information, necessary to create new unit.

<b>Returns: </b> None

##### disband_units(player, deficit)
<b>Description: </b>
Medium-level method removing units the player's units in the chronological order, called during upkeep phase, when the player's supply is lesser than player's units supply cost.
Starting from the oldest unit, they are removed from the simulation within a loop, which stops until all units are removed or the supply deficit surprasses zero.

<b>Args: </b>
- player: str - unique identifier of the player who's units are being disbanded.
- deficit: int - the negative integer representing the missing supplies.

<b>Returns: </b> None 

##### calculate_supplies(player)
<b>Description: </b>
Low-level method calculating the supply surplus/deficit based on the controlled regions and units. 
The value is calculated as sum of all controlled regions supply incomes minus sum of all units upkeep costs. 
Called during the upkeep phase to decide whether the player ought get to choose new units to recruit, or get some of their units disbanded.

<b>Args: </b>
- player: str - unique identifier of the player who's units are being disbanded.

<b>Returns: </b> 
- supply_balance: int - the difference between supply income and supply costs.

##### add_player_orders(player, order_list)
##### execute_order(order)
##### execute_players_orders()
##### process_orders_list()
##### verify_faithfulness(order_list)
##### verify_orders_uniqueness(orders_list)
##### verify_unit_ownership(player, orders_list)
##### verify_correctness(orders_list)
##### split_orders(orders_list)
##### add_unit(unit_type, owner, q, r, s)
##### remove_unit(unit)
##### push_chain(unit1, unit2)
##### place_unit(unit, tile)
##### move_unit(unit, dq, dr, ds)
##### attack_unit(unit1, unit2)
##### support_unit(unit1, unit2)
##### get_players_scores()
##### is_upkeep_round()
##### get_round_data()

## 5. Functions