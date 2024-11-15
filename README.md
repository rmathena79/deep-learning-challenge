# deep-learning-challenge
Data Analytics Boot Camp Module 21

# Report

## Overview
The purpose of this analysis is to describe an effort to use deep learning to create a neural network to predict loan success.

## Results

Preprocessing (Data Cleaning)

Compiling, Training, and Evaluating the Model

The final neural network contained 5 dense layers total:
1. 41 neurons with "gelu" activation
1. 41 neurons with "mish" activation
1. 41 neurons with "relu6" activation
1. 41 neurons with "tanh" activation
1. 1 neuron with "sigmoid" activation -- the output layer

Note the activations were selected based one some Keras Tuner runs, but very similar results were attained with other activations. I do not actually know what the activations means and cannot comment on which would be more appropriate, except for "sigmoid" which is explicitly meant to indicate the probability of matching 1 of 2 possible targets.

I was not able to reach the target 75% accuracy; I reached  only 73.3% accuracy. I actually reached this accuracy fairly quickly, mainly by using a nontrivial network (layers with various activiations and 30+ units each) and allowing more Application Types and Classifications to be categorized. This is the total list of what I tried:

Dropping Columns:
"SPECIAL_CONSIDERATIONS": Dropped because there are very few "special" loans, and it seems likely that each special consideration is distinct and not captured in the data. Dropping it seemed to improve accuracy.
"ASK_AMT": Dropped because the large cluster of 5000-value rows might have too much impact. The change to accuracy was too small to judge.

"STATUS": Dropped to see what would happen. Dropping it seemed to reduce accuracy.
"INCOME_AMT": Dropped to see what would happen. Dropping it seemed to reduce accuracy.
"APPLICATION_TYPE": Dropped to see what would happen. Dropping it reduced accuracy dramatically.
"CLASSIFICATION": Dropped to see what would happen. Dropping it reduced accuracy.
"AFFILIATION": Dropped to see what would happen. Dropping it reduced accuracy.
"USE_CASE": Dropped to see what would happen. Dropping it reduced accuracy.
"ORGANIZATION": Dropped to see what would happen. Dropping it reduced accuracy.

Adding New Columns:
"INCOME_MID": This is derived from the INCOME_AMT column, converting it from categorical to numeric. It did not help.
"INCOME_ASK_RATIO": The ratio of INCOME_MID to ASK_AMT. It did not help.

Allowing More Categories:
I reduced the cutoff for values_counts() values for both APPLICATION_TYPE and CLASSIFICATION, allowing more categories of each. This improved accuracy.

Re-Splitting:
Subjectively, the accuracy seemed "stuck" at 73.4% for a while and I wondered if my train_test_split() call happened to produce imbalanced training and test sets. I tried several other seed values without significantly impacting accuracy, effectively proving myself wrong.

I also added code to show the values_counts() for each solumn in the final training and test sets, to get a rough estimate of whether the split was good.

Hyperband Tuning:
I used Keras Tuner to iterate through a variety of network compositions. I did not have the resources to run through a huge number of combinations, but varying number of layers, units per layer, and activation, I got a sense of what helped. I settled on gelu, tanh, relu6, mish, and silu as the most useful activations, with at least 3 layers of at least 10 units each.

Though tuning suggested those solutions, it also showed a very persistent tendency to settle at about 73.0% - 73.4% accuracy with many combinations of network sizes and activations. This suggests that merely changing the network composition will not get past that threshold.

Many Neural Network Configurations:
I will not attempt to describe the variety of network compositions I tried. There were a lot. The final configuration is described above.

Saving the Best Model:


Multiple Accuracy Metrics:
Noting that fit() takes multiple accuracy metrics and combines them, I tried including three major accuracy metrics to maximize: Accuracy, F1Score, and AUC (Area Under Curve). This may have slightly improved the final result by detecting small improvements, but I suspect that in this case (basically binary classification) all the metrics are functionally equivalent.

Different Optimizers:
Since I don't really understand what optimizers do or how to pick one over another, I looped through them all, creating a fresh network and training it with the optimizer. These are the results I got:

{'adadelta': 0.6497958898544312, 'adafactor': 0.7356268167495728, 'adagrad': 0.7310787439346313, 'adam': 0.7364431619644165, 'adamw': 0.7367929816246033, 'adamax': 0.7367929816246033, 'ftrl': 0.5282799005508423, 'lion': 0.5282799005508423, 'nadam': 0.7346938848495483, 'rmsprop': 0.736093282699585, 'sgd': 0.7366763949394226}

Adjusting Learning Rate:

