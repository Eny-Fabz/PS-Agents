# ICS 3202: Artificial Intelligence
Practical Exercise 3: Designing a Searching Agent

## Group Members
Fabunmi, Eniola Faithfulness 167925 <br>
Maina, Tiffany Wanjiru 189592 <br>
Ogwayo, Emma Awuor 189923 <br>

## Question 1

### 1.1 The PEAS Framework for the Self-Driving Trailer Agent
From class discussions we learnt that the PEAS framework is designed to fully specify the setting of an intelligent agent; an intelligent agent is simply a system that observes its environment and makes decisions to achieve specific goals. So the intelligent agent we are considering is a **self-driving trailer** transporting cargo across Kenya's Highway Network from an initial state (**Nairobi**) to a goal state (**Kisumu**).

1. **Performance Measure** - This describes how the success of an agent is evaluated. In this case this would include that there is:
   - Safe transit and delivery of cargo from the initial state to the final state. Thus no collisions or traffic violations.
   - The trailer must reaching the correct destination town, Kisumu. If it arrives at another town and stops there it has "failed".
   - The agent should minimize the total path cost (shortest distance travelled in kilometres and least fuel consumption). It is not successful if it goes in circles when there is a direct and straightforward path available
   - Similarly the agent should minimize the total travel time while still observing and following traffic laws.

2. **Environment** - This refers to everything surrounding an agent that it observes and interacts with all while carrying out its tasks. For the self-driver trailer agent this includes:
   - The Kenya's Highway Network spanning Nairobi, Central Highlands, Rift Valley, and Lake Victoria Basin and road infrastructure such as roundabouts, speed bumps, potholes, traffic lights road signs e.t.c
   - Other road users such as all other forms of transportation e.g personal vehicles, motorcycles, buses e.t.c and pedestrians or possibly animals.
   - Varying weather and visibility conditions e.g rainfall, heavy fog e.t.c.

4. **Actuators** - Now these are the actual components or tools the agent uses to perform actions and interact with its environment:
   - Power steering mechanism.
   - Accelerator/throttle system and braking system.
   - Horn, hazard lights, turn indicators, headlights, and windshield wipers.
   - On-board display screen.

5. **Sensors** - These are the components that collect information from the environment and provide it for the agent:
   - GPS receiver for location tracking such as the current county the vehicle is in
   -  Cameras / Computer Vision sensors for observing the road
   -  Radar for detecting surrounding vehicles and obstacles
   -  Speed and distance sensors for monitoring movement


### 1.2 Classification of the Agent's Environment (5 Definitions)

1. **Partially Observable (vs. Fully Observable):**
   The trailer cannot obtain complete information about everything around it at a single point in time. For example, while the vehicle is departing from Nairobi, its sensors cannot directly observe a traffic jam in Nakuru. Furthermore, Sensors may miss hidden hazards, and visibility can be poor.
   
2. **Stochastic (vs. Deterministic):**
   Outcomes are uncertain because other road users, weather and unexpected events are not entirely under the trailer's control. There could be unpredictable external occurrences such as sudden braking by another vehicle ahead or an animal suddenly running across the road.
   
3. **Sequential (vs. Episodic):**
   Each driving decision affects later decisions and available actions. For example, choosing a road changes the trailer's subsequent position and available routes.
   
4. **Dynamic (vs. Static):**
   The highway environment continues to change while the self-driving trailer is decides on its next course of action. Other vehicles keep moving, traffic lights change, and pedestrians walk across the road while the agent computes its next control action.
   
6. **Multi-Agent (vs. Single-Agent):**
   The self-driving trailer operates in a shared road network alongside numerous other agents whose actions can affect its journey. These other agents act either competitively (competing for road space/lanes) or cooperatively (yielding way and obeying traffic rules).


## Question 2: Directed Graph and Abstracted Search Tree

### 2.1 Directed Graph of Kenya's Highway Network
The directed graph below models the state space across the **Nairobi**, **Central Highlands**, **Rift Valley**, and **Lake Victoria Basin** regions shown on the google map that was provided for us. 

* The **Nodes** represent states (cities and towns).
* **Directed Edges** represent moving from one town toward the next, labelled with their approximate road distance (step cost in km).

<img width="5945" height="1579" alt="mermaid-diagram-2026-10-17-002804" src="https://github.com/user-attachments/assets/00c6d783-ab4a-442f-9be6-3d455fe47fe2" />


### 2.2 Search Tree (Applying Abstraction)
**How Abstraction is Applied:**
In problem formulation, **abstraction** removes irrelevant details, states, and actions that do not contribute effectively to reaching the goal state (**Kisumu**) from the initial state (**Nairobi**):

1. **Removing branches that lead away from Kisumu:** We left out any routes that led away from Kisumu, such as Embu, Timau, Nanyuki and Isiolo, as well as dead-end branches such as Kabarnet and Kilgoris. These routes were not needed for the main journey from Nairobi to Kisumu.

2. **Leaving out smaller stops:** We also left out smaller stops along the way, such as Ruiru between Nairobi and Thika, Olenguruone and Nyamira. This keeps the search tree simpler and focuses on the main towns and road junctions that could help the trailer get from Nairobi to Kisumu.

<img width="2476" height="1750" alt="mermaid-diagram-2026-10-17-003506" src="https://github.com/user-attachments/assets/2858b5a0-bd09-4e94-90b4-43e9332cda63" />


## Question 3: Path Cost Function & Optimal Path Calculation

### 3.1 Step Costs (Average Distances Between Towns via Google Maps Search)
The path cost function assigns the road distance in kilometres between connected towns along Kenya's Highway Network as the step cost:

| Highway Segment (From Town – To Town) | Road Distance (km) |
| :--- | :--- |
| Nairobi to Naivasha | 88 km |
| Naivasha to Nakuru | 69 km |
| Nairobi to Nakuru  | 157 km |
| Nakuru to Kericho  | 107 km |
| Nakuru to Molo | 47 km |
| Molo to Kericho | 64 km |
| Nakuru to Njoro | 24 km |
| Njoro to Molo | 35 km |
| Kericho to Kisumu | 83 km |
| Nairobi to Narok | 142 km |
| Narok to Bomet | 82 km |
| Bomet to Kericho | 77 km |
| Bomet to Kisii | 83 km |
| Kisii to Kisumu | 120 km |
| Nakuru to Eldoret | 156 km |
| Eldoret to Kapsabet | 46 km |
| Kapsabet to Kisumu | 87 km |
| Nairobi to Nyeri | 151 km |
| Nyeri to Nyahururu | 104 km |
| Nyahururu to Nakuru | 64 km |


### 3.2 Total Path Cost Evaluation for Candidate Routes
The total path cost for each route is calculated by adding the individual step distances (in km) from the initial state (**Nairobi**) to the goal state (**Kisumu**):

1. **Route 1: Nairobi -> Naivasha -> Nakuru -> Kericho -> Kisumu (via Highway A104 and B1)**
   * **Town-by-town calculation (via Naivasha):** 88 km + 69 km + 107 km + 83 km = **347 km**

2. **Route 2: Nairobi -> Naivasha -> Nakuru -> Molo -> Kericho -> Kisumu**
   * **Town-by-town Calculation:** 88 km + 69 km + 47 km + 64 km + 83 km = **351 km**

3. **Route 3: Nairobi -> Naivasha -> Nakuru -> Njoro -> Molo -> Kericho -> Kisumu**
   * **Calculation:** 88 km + 69 km + 24 km + 35 km + 64 km + 83 km = **363 km**

4. **Route 4: Nairobi -> Narok -> Bomet -> Kericho -> Kisumu**
   * **Calculation:** 142 km + 82 km + 77 km + 83 km = **384 km**

5. **Route 5: Nairobi -> Narok -> Bomet -> Kisii -> Kisumu**
   * **Calculation:** 142 km + 82 km + 83 km + 120 km = **427 km**

6. **Route 6: Nairobi -> Naivasha -> Nakuru -> Eldoret -> Kapsabet -> Kisumu**
   * **Calculation:** 88 km + 69 km + 156 km + 46 km + 87 km = **446 km**

7. **Route 7: Nairobi -> Nyeri -> Nyahururu -> Nakuru -> Kericho -> Kisumu**
   * **Calculation:** 151 km + 104 km + 64 km + 107 km + 83 km = **509 km**


### 3.3 Optimal Path Recommendation
So now, our optimal solution for the agent is essentially the path sequence that achieves the goal state with the **lowest total path cost**:

* **Optimal Path:** **Nairobi -> Naivasha -> Nakuru -> Kericho -> Kisumu** 
* **Optimal Path Cost:** 
  * **347 km** (when summing the intermediate highway segments via Naivasha: 88 + 69 + 107 + 83 = 347 km)

Among all the route calculations, **Nairobi -> Naivasha -> Nakuru -> Kericho -> Kisumu** yields the minimum distance compared to the Narok–Bomet route (384 km), the Narok–Kisii route (427 km), the Eldoret–Kapsabet route (446 km), and the Nyeri–Nyahururu route (509 km), making it the optimal path for the self-driving trailer to execute.


## Groupwork Evidence: Meeting Minutes
* **Date of Meeting:** 16th October 2026
  
* **Platform:** Online (Google Meet) & GitHub Collaboration
  
* **Attendance:**
  1.  Fabunmi, Eniola Faithfulness 167925 - Present (Created repository, compiled Question 1 PEAS & Environment definitions, Reviewed formatting, verified forks, and proofread `design.md)
  2. Maina, Tiffany Wanjiru 189592 - Present (Developed Question 2 Directed Graph & Search Tree with abstraction)
  3. Ogwayo, Emma Awuor 189923 - Present (Calculated Question 3 Google Maps town distances and path costs)

* **Agenda & Resolutions:**
  1. Reviewed the lecture slides on Intelligent Agents and Problem-Solving Agents.
  2. Formulated the PEAS description and classified the 5 environment properties for the self-driving trailer o
  3. Mapped the nodes across Nairobi, Central Highlands, Rift Valley, and Lake Victoria Basin into a directed graph and applied abstraction to prune non-optimal eastern/dead-end branches for the search tree.
  4. Researched using google's feature and calculated the cumulative kilometric step costs across all major corridors from Nairobi to Kisumu and verified that the Nairobi–Naivasha–Nakuru–Kericho–Kisumu route yields the lowest cost.
  5. All present members forked the main `PS-Agents` repository as required.
