# Learning gene modules

This project contains the code for simulating gene expression profiles and learning gene module networks from the generated data.

## Generating simulated data

We simulate gene expression profiles in the following manner:

  1. First generate N pre-stimulus profiles x by a Poisson random variable, with
*s* samples per unique experiment. Each experiment corresponds to a random vector of expected values (lambda).
  2. Generate random tensor of rank L and order d by using a shallow decomposition.
  3. Simulate post-stimulus profiles y according to the random tensor and x. Each y is a random poisson variable: Y~ Poisson(M.dot(A(x)) + b), where b is a bias vector and M is the weight matrix for the module A(x).

To generated simulated data, run:
`python generate_data.py N J G d k L s file_label`.

The existing data in the directory was generated using the command:
`python generate_data.py 2000 12 20 3 3 1 20 poiss`.

Arguments:
  1. N = Number of samples
  2. J = Dimension of pre-stimulus profiles
  3. G = Dimension of post-stimulus profiles
  4. d = order of tensor
  5. L = rank of tensor
  6. s = number of samples per experiment
  7. file_label = prefix for output files.

Output files:
  1. Training and testing sets: file_label + 'trainx', file_label + 'trainy', file_label + 'testx', file_label + 'testy'
  2. Model files:
    i. file_label + 'T' stores the weights and vectors that generate the random tensor.
    ii. file_label + 'module_effects' stores the weights M and biases b.
    ii. file_label + 'activations' stores the offsets of the sigmoid functions.

## Learning gene module networks

To learn the correct structure of the gene modules, we have implemented a convolutional neural network model in Tensorflow.

### Quickstart:

Run the command `python run_model.py file_label` using the file_label from above.

Output:
file_label + 'data'. This is a csv file that contains information on how the learned model performed every 100 iterations. The columns in this file are:
  1. ind: unique identifier for a trial.
  2. snr: signal_to_noise ratio for initialized weights. If empty, the initialized weights were completely random.
  3. lr: learning rate used in algorithm.
  4. step: iteration number
  5. train_loss: value of loss function
  6. train_fit: 1 - |y - pred|/|y|
  7. test_loss: loss function for testing data
  8. test_fit: fit for testing data
  9. model_fit:
  10. redundancy_fit: how well model recovers functional redundancies.

Relevant files:
  1. run_model.py: wrapper code to train and test the model.
  2. model_relu.py: code for implementing the CNN.
  3. model_metrics.py: code for various metrics to evaluate the model.

For detailed explanations of the metrics in the csv file and the implementation of the model, refer to the notes and the code files.
