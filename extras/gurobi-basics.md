# Solver-GUROBI-Basics

The **Gurobi** Optimizer is a commercial optimization solver for linear programming (LP), quadratic programming (QP), quadratically constrained programming (QCP), mixed integer linear programming (MILP), mixed-integer quadratic programming (MIQP), and mixed-integer quadratically constrained programming (MIQCP). 

Gurobi was founded in 2008 and is named for its founders: Zonghao **Gu**, Edward **Ro**thberg and Robert **Bi**xby. Bixby was also the founder of CPLEX, while Rothberg and Gu led the CPLEX development team for nearly a decade. Another person should be mentioned at this point, Dr. Tobias Achterberg. He is one of the founder of the optimization solver SCIP and . He joined IBM CPLEX after his PhD and then moved to GUROBI. He is said to have done so much for GUROBI that he even made the software more expensive.

The Gurobi Optimizer supports a variety of programming and modeling languages including C++, Java, Python, Matlab and so on. Even though, in my opinion, Python is best suited for our research. Matlab is both OK, however, Matlab has gradually fallen behind The Times. SO, **Life's pathetic, let's *Pythonic***.



### Some Usual APIs

The following shows some commonly used Python APIs provided by Gurobi.

+ Model.setObjective(expr, sense=None)
+ Model.addVar(lb=0.0, ub=GRB.INFINITY, obj=0.0, vtype=GRB.CONTINUOUS, name="", column=None)
+ Model.addVars(*indices, lb=0.0, ub=GRB.INFINITY, obj=0.0, vtype=GRB.CONTINUOUS, name="")

+ Model.addConstr(lhs, sense=None, rhs=None, name="")
+ Model.addConstrs(generator, name="")
+ Model.optimize(callback)
+ Model.write(filename)
+ Model.setParam()



In this report, the 0-1 knapsack problem is employed to demonstrate how to call Gurobi via Python. 



### 0-1 Knapsack Problem

Given a set of items numbered from 1 up to $n$, each with a weight $w_i$ and a value $v_i$, along with a maximum weight capacity $W$. Informally, the problem is to maximize the sum of the values of the items in the knapsack so that the sum of the weights is less than or equal to the knapsack's capacity.
$$
\max \left\{\pmb{v^Tx} \ | \ \pmb{w}^T\pmb{x} \leq W, \pmb{x} \in \{0, 1\}^n\right\}
$$
In general, we may need to determine the following three things:

1. Input

2. Model

3. Output

Based on this, we may design the code as:

+ exp: the entrance of the code

+ gen_param: generate parameters

+ export_param: output the generated parameters

+ optimize: model and optimization

+ export_solution: output the solution

Thus, a simple Python demo can be:

```python
"""
__project_ = 'DepotOne'
__file_name__ = 'KnapsackDemo1'
__author__ = 'CO2MAKER'
__time__ = '2021/9/29 10:41 AM'
__product_name = PyCharm
# Practice makes perfect. 
"""
from random import randint
from gurobipy import *


def export_solution(sol: list, file_path: str) -> None:
    """
    Export the solution

    Args:
        sol: the solution
        file_path: the path to store solution

    Returns:
        None
    """
    if not sol:
        print("The solution is [].")
        return None

    with open(file_path, 'a') as f:
        f.write("\nSolution:\n")
        line = "{0}".format(sol[0])
        for i in range(1, len(sol)):
            line += ","
            line += str(sol[i])
        f.write(line)


def export_param(value: list, weight: list, file_path: str) -> None:
    """
    Write the parameter to file.

    Args:
        value: randomly generated value
        weight: randomly generated weight
        file_path: the path to store parameters

    Returns:
        None
    """
    with open(file_path, 'w') as f:
        f.write("Value,Weight\n")
        for i in range(len(value)):
            line = "{0},{1}\n".format(value[i], weight[i])
            f.write(line)


def gen_param(item_num: int, file_path: str) -> tuple:
    """
    Generate parameters randomly

    Args:
        item_num: the number of items
        file_path: the path to store parameters

    Returns:
        tuple, (value, weight)
    """
    value = [randint(1, 10) for _ in range(item_num)]
    weight = [randint(1, 5) for _ in range(item_num)]
    export_param(value, weight, file_path)

    return value, weight


def optimize(item_num: int, value: list, weight: list, capacity: int) -> list:
    """
    Optimization

    Args:
        item_num: the number of items
        value: value list
        weight: weight list
        capacity: knapsack capacity

    Returns:
        solution list
    """
    sol = []
    try:
        model = Model("Knapsack")
        x = model.addVars([i for i in range(n)], vtype=GRB.BINARY)
        # objective
        model.setObjective(quicksum(x[i] * value[i] for i in range(item_num)), sense=GRB.MAXIMIZE)
        # constraints
        model.addConstr(quicksum(weight[i] * x[i] for i in range(item_num)) <= capacity)
        # optimize
        model.optimize()
        # solution
        if model.status == GRB.Status.OPTIMAL:
            obj = model.objVal
            sol = [x[item].x for item in x.keys()]
            print("The objective is {0}".format(obj))
            print("The solution is {0}".format(sol))

    except GurobiError as e:
        print(e)

    return sol


def exp(item_num: int, capacity: int, file_path: str) -> None:
    """
    Experiment

    Args:
        item_num: the number of items
        capacity: knapsack capacity
        file_path: the path to store solution

    Returns:
        None
    """
    value, weight = gen_param(item_num, file_path)
    sol = optimize(item_num, value, weight, capacity)
    export_solution(sol, file_path)


if __name__ == '__main__':
    n = 10
    W = 10
    path = "./inst-10-10.csv"
    exp(n, W, path)

```

And if you prefer *Object Oriented* style, 

+ Parameter: maintaining the model parameters
+ Knapsack: maintaining the modeling and optimization
+ Experiment: maintaining the numerical experiment

Thus the code is omitted as:

```python
"""
__project_ = 'DepotOne'
__file_name__ = 'KnapsackDemo2'
__author__ = 'CO2MAKER'
__time__ = '2021/9/29 2:42 PM'
__product_name = PyCharm
# Practice makes perfect. 
"""
from random import randint
from gurobipy import *


class Parameter:

    """ Parameter """

    def __init__(self, item_num: int, capacity: int):
        self.item_num = item_num
        self.capacity = capacity
        self._gen_value()
        self._gen_weight()

    def _gen_value(self) -> None:
        """ generate value """
        self.value = [randint(1, 10) for _ in range(self.item_num)]

    def _gen_weight(self) -> None:
        """ generate weight """
        self.weight = [randint(1, 5) for _ in range(self.item_num)]

    def export_param(self, file_path: str) -> None:
        """ write parameters to file """
        with open(file_path, 'w') as f:
            f.write("Value,Weight\n")
            for i in range(self.item_num):
                line = "{0},{1}\n".format(self.value[i], self.weight[i])
                f.write(line)


class Knapsack:

    """ Knapsack Problem """

    _silent = False
    _x = None
    _model = None

    def __init__(self, param: Parameter):
        self.param = param
        self.obj = 0
        self._sol = []
        self._modeling()

    def set_silent(self):
        """ set silent """
        self._silent = True

    def get_objective(self) -> float:
        """ get objective value """
        return self.obj

    def get_solution(self) -> list:
        """ get solution """
        return self._sol

    def _modeling(self) -> None:
        """ modeling """
        try:
            self._model = Model("Knapsack")
            self._initialize_var()
            self._set_objective()
            self._add_constraints()
        except GurobiError as e:
            print(e)

    def _initialize_var(self):
        """ initialize variables """
        item_num = self.param.item_num
        self._x = self._model.addVars([i for i in range(item_num)], vtype=GRB.BINARY)

    def _set_objective(self) -> None:
        """ set objective function """
        item_num = self.param.item_num
        value = self.param.value
        self._model.setObjective(quicksum(value[i] * self._x[i] for i in range(item_num)), sense=GRB.MAXIMIZE)

    def _add_constraints(self) -> None:
        """ add constraints """
        item_num = self.param.item_num
        weight = self.param.weight
        capacity = self.param.capacity
        self._model.addConstr(quicksum(weight[i] * self._x[i] for i in range(item_num)) <= capacity)

    def _obtain_solution(self) -> None:
        """ obtain solution """
        self._sol = [self._x[item].x for item in self._x.keys()]

    def _print_solution(self) -> None:
        """ print solution """
        print(self._sol)

    def optimize(self):
        """ optimize """
        try:
            if self._silent:
                self._model.setParam('OutputFlag', 0)
            self._model.optimize()
            if self._model.status == GRB.Status.OPTIMAL:
                self.obj = self._model.objVal
                self._obtain_solution()
                if not self._silent:
                    self._print_solution()
            else:
                print("The solution status is not optimal. Please check.")

        except GurobiError as e:
            print(e)

    def export_solution(self, file_path: str) -> None:
        """ export solution """
        if not self._sol:
            print("The solution is [].")
            return None

        with open(file_path, 'a') as f:
            f.write("\nSolution:\n")
            line = "{0}".format(self._sol[0])
            for i in range(1, len(self._sol)):
                line += ","
                line += str(self._sol[i])
            f.write(line)


class Experiment:

    """ Experiment """

    def __init__(self, exp_num=1):
        self._exp_num = exp_num

    def _standard_out(self):
        pass

    def _marginal_out(self):
        pass

    def start(self) -> None:
        """ start the experiment """
        n = 10
        W = 10
        for exp in range(self._exp_num):
            file_path = "./inst-{0}-{1}-{2}".format(exp, n, W)
            param_ = Parameter(n, W)
            param_.export_param(file_path)
            knapsack_ = Knapsack(param_)
            knapsack_.optimize()
            knapsack_.export_solution(file_path)


if __name__ == '__main__':

    exp_ = Experiment()
    exp_.start()

```



> In my opinion, for simple problems, we do no need to write the code in OO style. But, OO style is much clear than PO. Since in OO style, *model*, *parameter* and  *experiment* are isolated. In addition, in PO, we have to take careful consideration of the design of the function parameters. 



But, just to be clear, mathematical programming is not always the best way. This problem can be solved to optimal by Dynamic Programming within $O(nW)$. We also need to open our minds.