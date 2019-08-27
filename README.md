ANN (Artificial Neural Network) function for plumed
====================

This is the repository for plumed function.  It implements `ANN` class, which is a subclass of `Function` class.  `ANN` class takes multi-dimensional arrays as inputs for a fully-connected feedforward neural network with specified neural network weights and generates corresponding outputs.  The `ANN` outputs can be used as collective variables, inputs for other collective variables, or inputs for data analysis tools.  

## Dependency

- [plumed](https://github.com/plumed/plumed2)

## Installation

Place file `ANN.cpp` under `src/function` directory of plumed, then proceed to install plumed.  `ANN` class will be built as part of building process of plumed package.

## Usage

It is used in a similar way to [other plumed functions](https://www.plumed.org/doc-v2.5/user-doc/html/_function.html).  To define an `ANN` function object, we need to define following keywords:

- `ARG` (string array): input variable names for the fully-connected feedforward neural network

- `NUM_LAYERS` (int): number of layers for the neural network

- `NUM_OF_NODES` (int array): number of nodes in all layers of the neural network

- `LAYER_TYPES` (string array): types of activation functions of layers, currently we have implemented "Linear", "Tanh", "Circular" layers, it should be straightforward to add other types as well

- `COEFFICIENTS_OF_CONNECTIONS` (numbered keyword, double array): this is a numbered keyword, `COEFFICIENTS_OF_CONNECTIONS${i}` is the **flattened** array of the weights connecting nodes in layer `i` and layer `(i+1)`.  An example is given in the next section.

- `VALUES_OF_BIASED_NODES` (numbered keyword, double array): this is a numbered keyword, `VALUES_OF_BIASED_NODES${i}` is the array of the bias for nodes in layer `(i+1)`.

Assuming we have an `ANN` function object named `ann`, we use `ann.0, ann.1, ...` to access component 0, 1, ... of its outputs (used as collective variables, inputs for other collective variables, or data analysis tools).

## Examples

Assume we have an ANN with numbers of nodes being [2, 3, 1], and weights connecting layer 0 and 1 are

```
[[1,2],
[3,4],
[5,6]]
```

weights connecting layer 1 and 2 are

```
[[7,8,9]]
```

Bias for layer 1 and 2 are

```
[10, 11, 12]
```

and 

```
[13]
```

respectively.

All activation functions are `Tanh`.

Then if input variables are `l_0_out_0, l_0_out_1`, the corresponding `ANN` function object can be defined using following plumed script: 

```
ann: ANN ARG=l_0_out_0,l_0_out_1 NUM_LAYERS=3 NUM_OF_NODES=2,3,1 LAYER_TYPES=Tanh,Tanh  COEFFICIENTS_OF_CONNECTIONS0=1,2,3,4,5,6 COEFFICIENTS_OF_CONNECTIONS1=7,8,9  VALUES_OF_BIASED_NODES0=10,11,12 VALUES_OF_BIASED_NODES1=13
```

This plumed script can be generated with function `Plumed_helper.get_ANN_expression()` in [this](https://github.com/weiHelloWorld/plumed_helper/blob/master/plumed_helper.py) repository.  Following is the Python code using this function to generate the script above:

```Python
from plumed_helper import Plumed_helper
ANN_weights = [np.array([1,2,3,4,5,6]), np.array([7,8,9])]
ANN_bias = [np.array([10, 11, 12]), np.array([13])]
Plumed_helper.get_ANN_expression('ANN', node_num=[2, 3, 1], 
                                 ANN_weights=ANN_weights, ANN_bias=ANN_bias,
                                 activation_list=['Tanh', 'Tanh'])
```

## Contact

For any questions, feel free to contact weichen9@illinois.edu or open a github issue.
