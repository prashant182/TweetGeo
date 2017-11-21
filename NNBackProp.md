
# Neural network assignment 6


```python
from math import exp
from random import seed
from random import random
import numpy as np
import random 



def initialize_network(ni, nh, no):
	network = list()
	#hidden_layer = [{'weights':[random.uniform(-0.5,0.5) for i in range(ni + 1)]} for i in range(nh)]
	hidden_layer =[{'weights':[-0.3378,0.1970,0.3099]},{'weights':[0.2771,0.3191,0.1904]},{'weights':[0.2859,-0.1448,-0.0347]},{'weights':[-0.3329,0.3594,-0.4861]}]
	network.append(hidden_layer)
	#output_layer = [{'weights':[random.uniform(-0.5,0.5) for i in range(nh + 1)]} for i in range(no)]
	output_layer = [{'weights':[-0.1401,0.4919,-0.2913,-0.3979,0.3581]}]
	network.append(output_layer)
	return network

def activate(weights, inputs):
	activation = weights[-1]
	for i in range(len(weights)-1):
		activation += weights[i] * inputs[i]
	return activation

def sigmoid(activation):
	return -1 + 2 / (1+ np.exp(-activation));

def forward_propagate(network, row):
	inputs = row
	for layer in network:
		new_inputs = []
		for neuron in layer:
			activation = activate(neuron['weights'], inputs)
			neuron['output'] = sigmoid(activation)
			new_inputs.append(neuron['output'])
		inputs = new_inputs
	return inputs

def sigmoid_derivative(output):
	return 0.5 * (1+sigmoid(output)) * (1-sigmoid(output));

def backward_propagate_error(network, expected):
	for i in reversed(range(len(network))):
		layer = network[i]
		errors = list()
		if i != len(network)-1:
			for j in range(len(layer)):
				error = 0.0
				for neuron in network[i + 1]:
					error += (neuron['weights'][j] * neuron['delta'])
				errors.append(error)
		else:
			for j in range(len(layer)):
				neuron = layer[j]
				errors.append(expected[j] - neuron['output'])
		for j in range(len(layer)):
			neuron = layer[j]
			neuron['delta'] = errors[j] * sigmoid_derivative(neuron['output'])

def update_weights(network, row, l_rate):
	for i in range(len(network)):
		inputs = row[:-1]
		if i != 0:
			inputs = [neuron['output'] for neuron in network[i - 1]]
		for neuron in network[i]:
			for j in range(len(inputs)):
				neuron['weights'][j] += l_rate * neuron['delta'] * inputs[j]
			neuron['weights'][-1] += l_rate * neuron['delta']

def train_network(network, train, l_rate, n_epoch, n_outputs):
	for epoch in range(n_epoch):
		sum_error = 0
		for row in train:
			outputs = forward_propagate(network, row)
			expected = [0 for i in range(n_outputs)]
			#expected[row[-1]] = 1
			sum_error += sum([(expected[i]-outputs[i])**2 for i in range(len(expected))])
			backward_propagate_error(network, expected)
			update_weights(network, row, l_rate)
		print('epoch=%d, learning rate =%.3f, error=%.3f' % (epoch, l_rate, sum_error))

seed(1)
dataset = [[-1,-1,-1],
           [-1,1,1],
           [1,-1,1],
           [1,1,-1]];
n_inputs = len(dataset[0]) - 1
#n_outputs = len(set([row[-1] for row in dataset]))
n_outputs = 1
network = initialize_network(n_inputs, 4, n_outputs)
train_network(network, dataset, 0.2, 1, n_outputs)
for layer in network:
	print(layer[])

```

    epoch=0, learning rate =0.200, error=0.199
    [{'output': 0.085317524588991445, 'weights': [-0.33641556763230712, 0.19702235579348504, 0.31616238433897781], 'delta': 0.0095304726487090881}, {'output': 0.37142429039953995, 'weights': [0.27335531621764747, 0.31907434688571623, 0.1693666043209926], 'delta': -0.030587263053110075}, {'output': 0.054960515530887433, 'weights': [0.28816159582584738, -0.14463142625839348, -0.022273024338362517], 'delta': 0.018708725307075146}, {'output': -0.22356004337438107, 'weights': [-0.33025529737705511, 0.35966500740447827, -0.46963350952376098], 'delta': 0.024294738889909726}]
    [{'output': 0.26208429017240875, 'weights': [-0.15043622917129129, 0.481891937182027, -0.2921043062194224, -0.37616951544385308, 0.27162573569865284], 'delta': -0.12881739623889266}]
    
