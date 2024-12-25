Let's understand the problem : 

Vitamins A and B are found in foods F1 and F2. 
One unit of food `F1 contains:`
  - 3 units of Vitamin A
  - 4 units of Vitamin B
  
One unit of `food F2 contains:`
  - 6 units of Vitamin A
  - 3 units of Vitamin B

**Costs :**
- Cost of one unit of F1 : 4
- Cost of one unit of F2 : 5

**Minimum Daily Requirements :**
- Vitamin A : 80 units
- Vitamin B : 100 units

It is assumed that excess amounts of Vitamins A and B are not harmful.
<h6>Variables</h6>

Let:
- $x$ — the quantity of food F1 to be used.
- $y$ — the quantity of food F2 to be used.

<h6>Constraints</h6>

For Vitamin A:

$3x + 6y \geq 80$

For Vitamin B:

$4x + 3y \geq 100$

**Non-negativity constraints:**

$x, y \geq 0$

<h6>Objective Function</h6>

Minimize the total cost :

$$\text{Minimize: } Z = 4x + 5y$$

<h6>Complete Model Formulation</h6>

$$
\begin{align*}
\text{Minimize: } & Z = 4x + 5y \\
\text{Subject to: } & \\
3x + 6y & \geq 80 \quad \text{(Vitamin A constraint)} \\
4x + 3y & \geq 100 \quad \text{(Vitamin B constraint)} \\
x, y & \geq 0 \quad \text{(non-negativity constraints)}
\end{align*}
$$

The model in excel looks like this : 

![[Screenshot 2024-10-03 at 23.16.10.png]]

![[Screenshot 2024-10-03 at 23.16.58.png]]

intger solution : 

![[Screenshot 2024-10-03 at 23.16.35.png]]

![[Screenshot 2024-10-03 at 23.17.47.png]]

<h6>Results</h6>

The optimal solution for this task is to produce 1.3 units of product F2 and 24 units of product F1. All of our constraints ( vitamin A and vitamin B ) are binding in such case. If we need integer solution , the solution will be 25 units of product F1 and 1 unit of product F2.

<h6>Insights from the sensitivity report </h6>

![[Screenshot 2024-10-01 at 19.37.11.png]]

**CONSTRAINTS**
Let's look at `shadow price` . Both of them are positive. Thus , if we produce one more unit of vitamin  , the total costs will grow by 0.53 for vitamin A or 0.6 for vitamin B respectively . 

`Allowable increase / deacrease`. We can `increase` our constraint ( demand ) by 120 units for `vitamin A` and by 6 for `vitamin B`. If we go outside the bounderies the shadow price will change. We can `decrease` our constraint ( demand ) by 5 units for `vitamin A` and by 60 for `vitamin B` without loosing the optimal solution without any changes in shadow price. 

**VARIABLE CELLS**
The costs of f1 can be increased by 2.6 and decreased by 1.5 without any need to recalculate the optimal solution. Also the costs of f2 can be increased by 3 and decreased by 2 respectively also without recalculating the optimal solution. If we go outside these boundaries , the calculated optimal solution will not be valid and has to be recalculated. 

<h6>Insights from the limits report </h6>

![[Screenshot 2024-10-01 at 19.55.14.png]]

Quite obvious that the lower limit are zeros for both f1 and f2. If we produce 0 of f1 , the costs according to the overall demand will be 85. If we produce 0 of f2 our costs will be 110. 

<h6>Conclusion</h6>

The optimal solution for this task is to produce 24 units of product F1 and 1.3 units of product F2. All constraints (for vitamins A and B) are binding in this case. If we need integer solution , the solution will be 25 units of product F1 and 1 unit of product F2.

<h6>Recommendations</h6>

1. It is recommended to continue producing 24 units of product F1 and 1.3 units of product F2 to achieve minimal costs for the vitamins.
2. Since the shadow price for both vitamins is positive, increasing the demand for vitamins A and B by one unit will increase the total costs by 0.53 and 0.6, respectively. This should be taken into account when planning production.
3. The constraints (demand) for vitamin A can be increased by 120 units and by 6 for vitamin B without losing the optimal solution. It is important to remember that changing these values beyond the specified boundaries will alter the shadow prices, requiring a reassessment of the planning.
4. The costs for product F1 can be increased by 2.6 or decreased by 1.5, while for F2 they can be increased by 3 or decreased by 2, all without the need to recalculate the optimal solution. Price fluctuations should be monitored, as changes outside these limits will necessitate a new analysis.
